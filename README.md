# Code-deploy

**This project is to Set up a CI/CD pipeline using Jenkins, Github, Amazon Codedeploy and AWS EC2.**

We are going to achieve the below objectives with the project

1. Deploy a simple web application to a server on a code push to a repository.
     - Automate the deployment of a simple web application to a server on a code push to a repository.
     - Implement a continuous integration and continuous delivery (CI/CD) pipeline to deploy a simple web application to a server on a code push to a repository.
2. The deployed web application should be reachable on any web browser.
     - Make the deployed web application accessible to any web browser.
     - Configure the deployed web application to be accessible to any web browser.
3. Make it scalable such that when load increases the number of servers scale up and down making sure the new servers have the updated code.
     - Implement a scalable architecture for the web application, so that the number of servers can scale up and down as needed.
     - Use a load balancer to distribute traffic to the web servers, and ensure that new servers are automatically provisioned and updated with the latest code when 
        the load increases.


Solution : 
* Jenkins will be used to automate the build, test, and deployment process. It will be triggered whenever there is a code push to the repository. Jenkins will build and test the web application, and then deploy it to the EC2 instances using CodeDeploy.

* An ALB will be used to distribute traffic to the EC2 instances. It will also be used to health check the EC2 instances and automatically remove unhealthy instances from the load balancer pool.

* An auto scaling group will be used to manage the EC2 instances. It will automatically scale up the number of EC2 instances when the load increases and scale down the number of EC2 instances when the load decreases.

* CodeDeploy will be used to deploy the web application to the EC2 instances. It will pull the latest code from the repository and deploy it to the EC2 instances.

* We use an S3 bucket in this project to store the web application code and artifacts.
This solution will provide a scalable and reliable way to deploy and manage your web application.

<img width="425" alt="image" src="https://github.com/Vicky2KR/Code-deploy/assets/115537512/76866f96-0cf0-4bbc-93fc-ecbc0bd70be4">


**Step 1:Create two IAM roles:**

1. AmazonEC2RoleforAWSCodeDeploy and AmazonS3FullAccess
2. Codedeplyrole
These roles will be used to deploy applications using AWS CodeDeploy.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/42e9879c-e78e-4e3b-b1c7-c9d53c991401)


**Step 2 :Create 4 ec2 instances with basic configuration (free tier) and keep the below  mentioned commands in the user data section.**


     
- Choose AMI: Amazon Linux 2
- Instance Type : t2.micro
- no. of Instances: 4


![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/ba0ff7dc-d853-4558-a138-d1bfdaf33346)

-Create security group to allow port 22(ssh) and port (80).

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/3ad5750f-0138-482a-8e5a-f3269d828a7d)

User data : This user data includes installation of some software like aws codedeploy, aws cli etc.


<pre>
```bash
#!/bin/bash
sudo yum -y update
sudo yum -y install ruby
sudo yum -y install wget
cd /home/ec2-user
wget https://bucket-name.s3.region-identifier.amazonaws.com/latest/install
sudo chmod +x ./install
sudo ./install auto
sudo yum install -y python-pip
sudo pip install awscli
```
</pre>

Note :Bucket-name is the name of the Amazon S3 bucket that contains the CodeDeploy Resource Kit files for your region. region-identifier is the identifier for your region.to find bucket name and region (https://docs.aws.amazon.com/codedeploy/latest/userguide/resource-kit.html#resource-kit-bucket-names)

* Once the instance are up and running assign the fist created IAM role to all 4 EC2 instances

**Step 3: Create an image from any of the instance which we will use in autoscaling group.**

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/3372fee6-b184-4cab-a054-af7fba214e93)

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/14eae174-70c9-48ad-b7ae-2b2143b99bf9)

**Step 4: Create target group and Application Load balancer.**

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/13928959-0603-4188-a5f1-558f45e253ac)

-In target group give path to /index.html which is the app file containing code over github.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/b37f0b7a-2da6-4174-adaa-8f857353c1e9)

- Add the 4 EC2 instance which we have created in the beginning as the targets.
  
![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/53fba247-87d6-4de0-8466-b8e766686b21)

* Create ALB :
![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/2e18f8c1-56eb-4baa-8925-ef03d390b4b5)

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/9967fb4a-429f-41e5-855e-ac1d8d885496)

-create one Security group for load balancer. Open port 80 for http traffic.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/b012c5c3-cc00-48b7-860a-7200113937e7)

-select the target group which we created.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/db9cb22a-aa50-4616-99ff-5f1a8210b8cf)

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/40d591ff-534e-4c62-8521-bfcf08166baa)

**Step 5: Create launch template and Autoscaling group.**

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/f89a13d4-5c2b-45e8-90ad-d8af236a48c3)

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/f6ad47ad-e4eb-4645-b896-c9c11d0cb657)


Note: create a new security group and open port 80(http) and 22(ssh) and attach it to launch template.
 
![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/a1cbe1c6-5fc6-42e4-8ab6-9d09a59c1044)

* Create Autoscaling group:

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/505c684d-cf21-4957-b146-b58778cdb13d)

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/680a7e8e-8932-41f5-9b42-e8c5346f2b4f)

-Select the load balancer existing group we have created in step 4.
![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/d816e626-f9fa-41a0-b856-d3935c74535d)

-Give Desired and max capacity to 2

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/a678a75a-bdca-4e5e-8a78-8223e1e06280)

**Step 6: Create a new EC2 instance and install Jenkins software and install git as well by using the command **yum install git -y****

-open port 8080 additionally  since Jenkins is accessible over that port.

**Step 7: Create Code Deploy**

* Create application:
- Application Name : code-deploy-app
-  Compute Platform : EC2/OnPremises


![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/70cbe42d-7ab0-42da-b8b6-91f3b8b5597b)

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/5b162c2e-cad9-478a-aa54-c374dc3d36db)

* Create Deploymentgroup:

- Enter a deployment group name: code-deploy-dg
- Service role ARN: 
- Deployment type: In-place
- Deployment configuration: CodeDeployDefault.AllAtOnce
- Environment configuration: Amazon EC2 AutoScaling groups


![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/fa8584de-e77f-4b1f-8b61-5b5946828b33)

-Select Amazon Ec2 ASG which we have Created earlier.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/93f323e4-33f7-4823-9efa-2e33ea09f339)


 -Select Application Load balancer which we have created

 ![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/5b9c22ad-c9bb-4219-be99-f4a13d19ca4e)

 
**Step 8:Create S3 bucket.**

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/e68f284c-0cf4-4f9a-b8b3-3c3ce292a1a3)

-create a folder in the s3 bucket.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/ad630366-3b43-4282-ba72-28101452032c)


**Now we will use Jenkins for Creating a pipeline that will use github as asource and will run the CodeDeploy.**

****Step 9:**Go to the Jenkins server.( you can access the jenkins server using **ip-address:8080**) and install suggested plugins.****

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/c520de27-f59c-463a-9289-5b19fe109192)

- Once the installation of plugins gets completed, create a profile by giving the required details.

-Install AWS Codedeploy plugin from available plugins section.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/6cd9abf4-66e2-4ebc-b333-c3fe835ea91a)

- Create a free style project.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/3fbaed87-c8d8-4017-9af1-8b17da0d8782)

-Configure the build.

  *In Source code management select git and provide your github repo link and make sure the files related to the project are available in repo.
    
![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/38d13d99-ced0-4484-8d48-2ee867aeb093)

<img width="639" alt="image" src="https://github.com/Vicky2KR/Code-deploy/assets/115537512/7ee126f7-d177-433f-8a60-ceeb3c3b18c6">


-In Build Triggers section select POLL SCM.

<img width="653" alt="image" src="https://github.com/Vicky2KR/Code-deploy/assets/115537512/2df5b9d9-4ccf-40ce-8566-30cb5269da76">


-Choose post-build-action as deploy an application  using AWSCodeDeploy.

<img width="151" alt="image" src="https://github.com/Vicky2KR/Code-deploy/assets/115537512/31f57097-bae2-4064-9ee4-92037616cbe1">

-Give all the details related the codedeploy application, deployment group and s3 bucket.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/8ad5005f-a12e-4e5a-9cb7-aba1c0840304)

-Provide access key and secret for authorization and save.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/c7bcc3e0-d59d-4412-a9e7-921dbb6aaf39)

-click on build now to runthe pipeline.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/b83959c2-5f41-40f9-ba08-27b541ed1933)

As soon as we will hit the Build Now button the job will run and trigger for code deploy deployment . we can see here it is in progress.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/9e598955-bb9c-4f9d-8ae4-073bcbba79b6)

and we can see that data is getting stored in the s3 bucket as well.

<img width="768" alt="image" src="https://github.com/Vicky2KR/CI-CD-using-Jenkins-and-AWS-Codedeploy/assets/115537512/6f49d099-99cf-4298-877a-e61b0320a1af">


**Step 10:Once the deployment is succeeded, Go to AWS Load balancer portal and go to that particular ELB we have Created and copy the DNS and paste it on browser , our application will be running.**

<img width="480" alt="image" src="https://github.com/Vicky2KR/CI-CD-using-Jenkins-and-AWS-Codedeploy/assets/115537512/6c892eac-16ed-4e7e-8e80-6bfb518bdca0">


**This is the first version of application.**

![image](https://github.com/Vicky2KR/CI-CD-using-Jenkins-and-AWS-Codedeploy/assets/115537512/0b31f353-cf3f-4f63-a64a-c45515265dd9)

**Now if i made any change to the code it will automatically reflect to the Web browser within some minutes.**

I have modified the file and saved it.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/8cdaa428-e461-44e8-984f-1618e696b7be)

Once we make changes to the file, the pipeline automatically triggers.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/2b31c94e-e1b1-4212-907a-e47f9d538ff5)

<img width="916" alt="image" src="https://github.com/Vicky2KR/Code-deploy/assets/115537512/5fdb57dd-8621-4305-a27f-d38a72a580f9">


If we open the dns link of load balancer now in the browser,we can see the changes.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/5529451e-9b37-4b76-9322-b414cb887b94)





























































