# Code-deploy

This project is to Set up a CI/CD pipeline using Jenkins, Github, Amazon Codedeploy and AWS EC2.

We are going to acheive the below objectives with the project

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

<img width="425" alt="image" src="https://github.com/Vicky2KR/Code-deploy/assets/115537512/76866f96-0cf0-4bbc-93fc-ecbc0bd70be4">


Step 1:Create two IAM roles: 
1. AmazonEC2RoleforAWSCodeDeploy and AmazonS3FullAccess
2. Codedeplyrole
These roles will be used to deploy applications using AWS CodeDeploy.

![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/ec37b4ad-9c17-40c9-b59f-b48d5bf1f76c)


![image](https://github.com/Vicky2KR/Code-deploy/assets/115537512/42e9879c-e78e-4e3b-b1c7-c9d53c991401)


Step 2 :Create 4 ec2 instances with basic configuration (free tier) and keep the below  mentioned commands in the user data section.


-Create security group to allow port 22(ssh) and port (80).















