
##  Multi Tier Web Application(Refactor)

The previous version of the project relied on virtual machines, where various services were hosted on separate AWS EC2 instances. Although the process was automated, it proved to be time-consuming. To address this, a refactoring of the services with AWS is being undertaken to enhance project efficiency.


##  Project Description

Previously, the project utilized virtual machines, with various services spread across different AWS EC2 instances. While the process was automated, it proved to be time-consuming.

To address this, we propose refactoring the services using AWS to enhance project efficiency. By leveraging AWS services, we can streamline and optimize the infrastructure, reducing setup and maintenance overhead. 

This approach offers scalability, flexibility, and cost-effectiveness. The migration may involve utilizing managed services, serverless architectures, and containerization to improve resource utilization and simplify deployment.

Overall, the goal is to leverage AWS's capabilities to enhance the project's performance and reduce operational complexities.
## Architecture

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-LiftAndShift/Arch_.png)

The project is undergoing a comprehensive infrastructure refactoring to optimize its architecture. The following enhancements are being implemented:

- Virtual machines running Tomcat are being replaced with AWS Elastic Beanstalk, streamlining the deployment and management process.
- Instead of using Nginx as a load balancer, AWS Elastic Beanstalk's built-in load balancing capabilities are being leveraged.
- Automated scaling is being transitioned from manual management of virtual machines to the scalability features offered by AWS Elastic Beanstalk.
- Storage efficiency is being improved by adopting AWS S3 or EFS (Elastic File System) as the preferred storage solutions.
- Self-managed databases are being replaced with Amazon RDS instances, simplifying database administration and ensuring scalability.
- Memcached is being replaced with AWS ElastiCache, a managed caching service, for better performance and scalability.
- Amazon ActiveMQ,a message broker is being utilized instead of RabbitMQ for messaging services.
- AWS Route 53 is being employed as the DNS service for efficient traffic management and routing.
AWS CloudFront, a content delivery network (CDN), is being integrated to enhance content distribution and improve user experience.
- These changes aim to capitalize on the flexibility, scalability, and managed services offered by AWS, resulting in optimized infrastructure and improved overall project performance.
## Deployment

**KeyPair and Security Groups**

- Generate a key pair for the Elastic Beanstalk instance.
- Create a security group for the backend services.
    - Add a dummy inbound rule initially and then update it to allow internal traffic flow.

**RDS**

- Create a subnet specifically for the RDS instance.
- Configure the Parameter Group, which contains settings and configurations for RDS.
- Inject the Parameter Group into the database.

**Elastic Cache (Replacement for Memcached)**

- Set up an Elastic Cache instance.
- Create the necessary parameters.
- Define a subnet for the cache.
- Create the cache.

**Amazon MQ (Replacement for RabbitMQ)**

- Create an Amazon MQ engine.
- Choose between a single cluster or cluster deployment for production.
- Configure the engine and create a username and password (remember to securely store them).

**Initialization of the Database**

- Save the RDS database endpoint for future reference.
- Launch an instance with MySQL installed.
    - Create a new security group for MySQL with SSH access.
    - Install either mysql-client (Ubuntu) or MariaDB (Amazon Linux or CentOS).
- Establish a connection between MySQL and the RDS database.
    - Inbound traffic for MySQL needs to be allowed in the backend security group.
- After successful connection, clone the repository in the MySQL instance and inject it into the RDS instance.

    - Switch to the aws-refactor branch.
    - Go to the project's DB folder and inject it into the RDS instance.


**Initialization of Elastic Beanstalk**

Configure the environment.
![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/1.png)

Environment variables.
![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/2.png)

Select the platform.
![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/3.png)

Choose the application platform.
![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/4.png)

Utilize a sample application for now; code will be added later.
Use custom configuration instead of a single instance, as some settings require specific configurations.
Configure Service Access
![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/5.png)

If the EC2 instance profile for Beanstalk is not created, manually create an IAM role (due to security changes in AWS).

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/6.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/7.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/8.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/9.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/10.png)
Monitor the NetworkOut metric to determine when scaling is necessary.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/11.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/12.png)

Later, add HTTPS (443) and /login.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/13.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/14.png)

Rolling updates are available in different types.

One option is the rolling update, where a percentage of instances are updated while the rest remain unaffected.
Verify the settings and create the environment.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/15.png)

Update Security Groups and ELB
After creating the environment, update the security group for the backend services to allow Beanstalk access.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/16.png)

Modify the Load Balancer

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/17.png)

Open the configuration -> Load Balancer.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/18.png)

Add a listener.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/19.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/20.png)

Add HTTPS and an SSL certificate.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/21.png)

Update the health check.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/22.png)

Change it to /login.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/23.png)

Enable stickiness for load balancing to maintain the user's connection to the same instance, if desired.
Once done, the health status may indicate severe.

**Build and Deploy the Artifact**

Update the RabbitMQ, Memcached, and SQL endpoints in the application.properties file.

Build the artifact using Maven.

Upload the artifact to the Elastic Beanstalk environment.
![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/24.png)

Once deployed, verify that the status indicates "OK."

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/25.png)

Once done, check if stickiness is enabled in the load balancer settings.
Update the environment's domain in the site's domain host.

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/26.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/27.png)

Now verify all the services.

**Content Delivery Network (CDN)**

CloudFront
Global customers require efficient content delivery, which can be expensive without caching.
- To address this, we use CloudFront. Create a Distribution

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/28.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/29.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/30.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/aws-refactor/Steps_Ss/31.png)

Adjust settings as needed, with minimal changes.

