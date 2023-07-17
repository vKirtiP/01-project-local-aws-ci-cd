
## Multi-Tier Web Application(Lift and Shift Operation)

This project involves the development of a multi-tier web application stack that will be deployed on the AWS cloud for production purposes. We plan to adopt a "Lift and shift" strategy for migration.
##  Project Description

The multi-tier web application comprises various services, including:

TOMCAT: We will utilize EC2 instances on AWS to host TOMCAT.

RABBITMQ: We will also use EC2 instances for hosting RABBITMQ.

MEMCACHE: Similar to TOMCAT and RABBITMQ, we will deploy MEMCACHE on EC2 instances.

MYSQL: We will utilize an EC2 instance to host the MYSQL database.

To replace these services with AWS offerings, we will make the following changes:

TOMCAT, RABBITMQ, MEMCACHE, and MYSQL will be hosted on individual EC2 instances.

NGINX, currently used for load balancing, will be replaced by Elastic Load Balancer (ELB).

For auto-scaling, we will employ AWS Auto Scaling, ensuring efficient VM scaling.

Shared storage will be provided by Amazon S3.
Route 53 will be used for the private DNS service.

AWS Identity and Access Management (IAM) will handle access and authorization management.

Amazon Certificate Manager (ACM) will be utilized for managing SSL/TLS certificates.
Elastic Block Store (EBS) will be used for persistent storage.

By implementing these changes, we aim to leverage various AWS services to enhance the scalability, reliability, and performance of our multi-tier web application stack hosted on the AWS cloud.
## Architecture

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/Arch_.png)

Users will access the web application by typing the URL, which will lead them to the endpoint specified in the hosting site's DNS configuration.

To establish a secure connection, users will utilize the HTTPS protocol and connect to the load balancer. The HTTPS certificate will be stored in the AWS Certificate Manager (ACM).

The application load balancer will only allow HTTPS traffic and will intelligently route incoming requests to the respective Tomcat instances.

Each Tomcat instance will have its own security group and will be managed by an auto-scaling group. The security group will be configured to permit traffic on port 8080 exclusively from the load balancer.

The IP addresses of the backend services, including Memcache, RabbitMQ, and MySQL instances, will be stored in the private DNS zone of Route 53.

Additionally, there will be an S3 bucket attached to the setup, which will serve as a storage location for software artifacts and related files.

## Deployment

Make sure the Amazon Certificate Manager has issued the DNS certificate for the website.

Create security groups for the Load Balancer:

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/1.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/2.png)

Create a security group for the Tomcat instance:

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/3.png)

Set up inbound rules to allow traffic from the Load Balancer on port 8080.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/4.png)

**Backend services:**

Create a security group for the backend services and define inbound rules accordingly.
![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/5.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/6.png)

Create key pairs for the instances:

Access the key pair section and create a key pair using .pem or ptty format.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/7.png)


Create instances for each service with userdata and bash script:

    - Launch an EC2 instance for SQL, memcache, and RabbitMQ.
    - Provide a name, choose an OS (CentOS in this case), select the key pair, and assign the backend security group.
    - Paste the bash script in the user data section.
    - Adjust inbound rules in the backend security group to allow SSH access from your own IP.
    - Login to the instance using SSH.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/8.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/9.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/10.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/11.png)

Update private IPs in Route 53 DNS zone:

Create a hosted zone and records for each service.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/12.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/13.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/14.png)

Build and deploy artifacts:

Ensure Maven and JDK 8 are installed.

Modify the application.properties file with the  private DNS IP of each service.

Build the project using Maven and verify the .war file in the target folder.

Create IAM user for AWS CLI and configure AWS CLI:

Create an IAM user with AWS S3 full access.

Configure AWS CLI using the downloaded CSV file.

Create an S3 bucket:

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/15.png)


Create an S3 bucket for storing artifacts.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/16.png)

Copy the artifact to the S3 bucket:

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/17.png)

Use AWS CLI to copy the artifact to the S3 bucket.

Create a role for S3 storage and attach it to the EC2 instance:

Create a role with S3 full access.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/18.png)

Attach the role to the EC2 instance.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/19.png)

Login to the Tomcat EC2 instance:

    - Remove the existing "root" file from var/lib/tomcat8/webapps/.
    - Install AWS CLI.
    - Copy the AWS S3 file from the S3 bucket to a temporary location.
    - Set up the load balancer:

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/20.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/21.png)

Create a target group for EC2 instances on port 8080.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/22.png)

Create an application load balancer.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/23.png)

Update DNS record in the domain host.


**Set up Auto Scaling Group (ASG) for Tomcat server:**

    - Create an AMI for the Tomcat instance.
    - Create a Launch Configuration.
    - Create an Auto Scaling Group.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/24.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/25.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/26.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/27.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/28.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/29.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/steps_ss/30.png)

Once you have completed these steps, the setup should be fully functional.
