# PythonDockerAWS
How to dockerize a python application and deploy on AWS Elastic Container Service

Overview of Amazon ECS and Amazon ECR

Amazon ECS is a highly scalable, fast container management service that makes it easy to run and manage Docker containers on a cluster of Amazon EC2 instances and eliminates the need to operate your own cluster management or worry about scaling management infrastructure

In order to reliably store Docker images on AWS, ECR provides a managed Docker registry service that is secure, scalable, and reliable. ECR is a private Docker repository with resource-based permissions using IAM so that users or EC2 instances can access repositories and images through the Docker CLI to push, pull, and manage images.

This article is consists of 4 major parts.

1.Creating a Repository on ECR (To push our docker image to ECR Cloud)

2.Dockerize the Python Application (Basic containerization but carefully read and understand each step of it)

3.Push it on ECR (Elastic Cloud Registry) Container

4.Deploy that container on an ECS (Elastic Container Service) node

Before moving on with my article, I assume that you have a basic knowledge about AWS and hope you guys have settled inn for a account so you can create a new tab and follow me on with each step

1. Create a Repository on ECR (Elastic Container Registry)

Install the AWS CLI — You can use the AWS command line tools to issue commands at your system’s command line to perform Amazon ECS and AWS tasks. This can be faster and more convenient than using the console. The command line tools are also useful for building scripts that perform AWS tasks.

To use the AWS CLI with Amazon ECR, install the latest AWS CLI version (Amazon ECR functionality is available in the AWS CLI starting with version 1.9.15). You can check your AWS CLI version with the aws — version command.

2. Open Powershell/Terminal

Type the following command to create a new repository called python-app-aws.

aws ecr create-repository --repository-name python-app-aws

Go to AWS Console and search for ECR in services.


And click on Repositories so you can see your repository has been created.


2. Dockerize the Python Application

Go to the following link to clone/download this python app that I’m going to dockerize. (Please ignore the DP :) )

https://github.com/kathpalrk/python-docker-ecs

1. app.py
2. Dockerfile
3. requirements.txt

Once you have finished setting up the above files, Let’s try to build this folder in docker as the very first step for containerization.

3. Push it on ECR (Elastic Cloud Registry) Container
** Ensure you have installed the latest version of the AWS CLI and Docker. For more information, see the ECR documentation.

*Open Powershell in Windows/ Terminal in Mac to do the ABTP!*

	A- Authenticate
	B- Build
	T- Tag
	P- Push

Retrieve the login command to use to authenticate your Docker client to your registry. Use AWS Tools for PowerShell:

Invoke-Expression -Command (Get-ECRLoginCommand -Region ap-southeast-2).Command


2. Build your Docker image using the following command. For information on building a Docker file from scratch see the instructions here. You can skip this step if your image is already built:

docker build -t python-app-aws .


3. After the build completes, tag your image so you can push the image to this repository: *Make sure to replace the name ID from your unique URL for the repository you have created*

docker tag python-app-aws:latest ID.dkr.ecr.ap-southeast-2.amazonaws.com/python-app-aws:latest

4. Run the following command to push this image to your newly created AWS repository:

docker push ID.dkr.ecr.ap-southeast-2.amazonaws.com/python-app-aws:latest

The word after colon (:) is the tag part so you can identify each version of pushes. For example; I’ve pushed two images called :latest and :v2

4. Deploy that container on an ECS (Elastic Container Service) node

I. Create a Task Definition.

Before you can run Docker containers on Amazon ECS, you must create a task definition. You can define multiple containers and data volumes in a task definition. For more information about the parameters available in a task definition, see Task Definition Parameters.

# To create a new task definition

Open the Amazon ECS console at https://console.aws.amazon.com/ecs/.
In the navigation pane, choose Task Definitions, Create new Task Definition.
On the Select compatibilities page, select the launch type that your task should use and choose the Next step.

*Select Fargate launch type.*

The Fargate launch type is not compatible with Windows containers

5. Follow these steps under the Fargate tab.

Important: In the containerDefinitions section of your task definition, specify the ECR image aws_account_id.dkr.ecr.region.amazonaws.com/repository:tag as the image property.

After you created the Task Definition, it should look like this on the dashboard. (I’ll highlight the important fields in Yellow color in case you missed those essential efforts in the documentation links I’ve posted above; You can double-check and guarantee that you are on the correct path as I am.)


Ensure that the container is attached to your task def. (Under image name; ECR URL should be displayed)

II. Create a Service to run the above task definition.

All services require some basic configuration parameters that define the service, such as the task definition to use [Which we want exactly], which cluster the service should run on, how many tasks should be placed for the service, and so on. This is called the service definition. For more information about the parameters defined in a service definition, see Service Definition Parameters.

This procedure covers creating a service with the basic service definition parameters that are required. After you have configured these parameters, you can create your service or move on to the procedures for optional service definition configuration, such as configuring your service to use a load balancer.

Click on this link and follow the steps to create a service for Fargate Launch type. (Create 3 replica task)

After you created the Service, it should look like this on the dashboard. (I’ll highlight the important fields in Yellow color in case you missed those essential efforts in the documentation links I’ve posted above; You can double-check and guarantee that you are on the correct path as I am.)

Note and understand the Service Type, Launch Type, Service Role.

Select Target Group which leads you to here.

Copy the DNS name and paste it on a new browser window and Voila!


Try to refresh the window several times and see how the Hostname getting changed and you can see three different hostnames which is according to the number of Replica tasks we have established in our service.


Thank you very much for your time on reading this post, This can be seriously difficult to understand in one read because trust me I had to spend days to gather all these information (We are data scientists, not DevOps engineers) before I successfully deployed this simple app on ECS so just be patient and progress by step by step. If you run into any errors, Let me know.

Cheers!


