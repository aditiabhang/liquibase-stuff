## DevOps Homework

Start date - Oct 8th, 2020

Time - 10:00 am CDT

----------------------------------------------
After skimming through all the tasks and understanding what is exactly expected in each task, I figured to start the homework by completing the base task that is required to complete the assignment. That is, Task 2 : Fix the Dockerfile and docker-compose files. Completing this task will allow me to run the application locally. 

### Notes for Task 2: Application

> Part 1: Decrease the Dockerfile size

First of all, I cloned the sre-homework repository from github to my local machine. Then build the docker image to see the actual size of the todo-api Docker image. Initially, the build took long time and threw many npm warnings related to numerous npm dependencies being deprecated. I did some googling to find a turn around on the warning and found the solution [here](https://stackoverflow.com/questions/62754354/npm-install-giving-error-after-so-many-warn) which suggested to clear the cache and do the install. I tried the solution and it worked. The image build was complete, although by taking some time. 

The actual docker image was 1.17GB which is enormous amount space consumption. This simply does not serve the docker purpose. I did some research on how to minimize the docker image size and found 2 ways to do so. 

1. Pull smaller image flavor from Docker hub (slim or alpine)
2. Multi-staging. 

I tried both ways. 

1. Smaller image: 

I pulled up smaller image **node:12-apline** by editing the Dockerfile. The result was fine, but it was not below 300MB.

<img width="706" alt="Screen Shot 2020-10-07 at 2 25 19 PM" src="https://user-images.githubusercontent.com/51350594/95381229-76c63e00-08ad-11eb-91b6-be9bb4f0b430.png">

2. Multi-staging the docker image.

To learn about multi staging in Dockerfile, I went over the Docker documentation and understood the basic on it. I implemented the multistaging by using one more FROM statement in your Dockerfile. First I named the first stage as **builder** and used this stage as a base stage using *COPY --from=builder*.  

After performing the docker build command, the multi-stage docker image was build succusfully. The new size is 270MB. 

<img width="714" alt="Screen Shot 2020-10-07 at 4 04 10 PM" src="https://user-images.githubusercontent.com/51350594/95387929-15a36800-08b7-11eb-813b-1d28d12c1659.png">

> Part 2: Fix docker-compose bugs

I encountered a lot of confusions, warnings and few error while solving this part. I was able to fix one bug the docker-compose file pretty soon. I did some online research on the contents of a docker-compose file. I figured out the url was incorrect, which I corrected. It worked fine. When I performed the **docker-compose up** it gave out an connection error and role not found error. I looked up online to understand the connection between api to db and came across many stackoverflow articles that guided me. I figured the missing environment variables in the services, which I added. I figured the solution for the connection error; had to mention the correct DB_HOST_NAME. Although, the error for role not found sustained. 

Finally, I went over the following projects files to understand what every line does: todo-api/Dockerfile, postgres-init.sql, package.json, and database.providers.ts file. This got me a strike. I changed the Dockerfile CMD by replacing the start:prod to start, and it worked like charm. 

I was able to run the todo application on localhost, I cross checked data items entry in the postgress from my terminal. Everything was working good for this task. Screenshots attached below.

------------------------------

I had no prior experience working or hands-on knowledge of Terraform. So, just to get an idea of it I tried to learn something online by doing research on Terraform reading blogs on medium, stackoverflow and watching few YouTube videos. Unfortunately, I realised it cannot be learned in few hours. So I decided to deploy the app using AWS Console, manually on EC2 instance by setting up the RDS DB instance, defining security group, setting Elastic Load Balancer and launching the application.

### Notes for Task 1, 3 & 4: AWS Deployment settings (Elastic Load Balancer & EC2 instance), security groups and AWS RDS database connection for production environment.

> AWS RDS for database 

For this task, I initally created a RDS Database. As the requirement was to allow access only from the EC2 instance, so I set the public accesibility of the RDS to **Publicly accessible** which will allow EC2 instances and devices outside the VPC tp can connect to the instance. Also allows to define the security groups for supported devices and instances.
For Database authentication, I chose Password and IAM database authentication, that authenticates using the database password and user credentials through AWS IAM users and roles.
I selected the **Postgres logs** as export log types, that will publish the logs to Amazon CloudWatch Logs.
Rest, kept everything default to stay under free tier.

> Security group

As per the requiments, I created a new security group with default vpc and the rules for inbound and outbound. Inbound rule set to Postgres type post 5342 to source anywhere. Outbound rule to HTTP type post 80.

> Launch EC2 Instance

I launched an Amazon Linux 2 AMI (HVM), SSD Volume Type Intance, by creating a new IAM user with the permission policy of EC2FullAccess, CodeCommitFullAccess, CodeDeployFullAccess, RDSPowerUserAccess. Rest everything set factors that come under free tier. 

`PS:` I have deployed a simple Dockerized application on EC2 instance by using the AWS CodeCommit, CodeDeploy and CodeBuild. Given, the assignment consists of docker compose, it is taking time for me to learn and implement the required settings.
