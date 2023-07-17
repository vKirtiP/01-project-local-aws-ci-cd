
# Continuous Integration using AWS

Implement an automated CI/CD pipeline using CodeCommit, CodeArtifact, CodeBuild, and CodeDeploy to build, test, and notify the status of each commit.




## Project Description

The project aims to establish an automated CI/CD pipeline using AWS services, including CodeCommit, CodeArtifact, CodeBuild, and CodeDeploy. The primary objective is to create an efficient and reliable process that builds and tests the codebase for every commit made to the repository.

The pipeline incorporates code artifact management, leveraging CodeArtifact to store and manage dependencies securely. CodeBuild is utilized to perform build and test operations, ensuring code quality and identifying any issues. 

Finally, CodeDeploy automates the deployment process, allowing seamless and consistent application releases. The pipeline is designed to provide timely notifications on the build status, enabling the development team to stay informed about the progress and take necessary actions.

By implementing this automated process, the project aims to enhance development efficiency, streamline deployments, and deliver high-quality software with reduced manual efforts.
## Architecture

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/ci-aws/arch_.png)
## Deployment

**Code Commit**

To set up Code Commit, follow these steps:

1. Go to the Code Commit service in the AWS Management Console.
2. Create a new repository for your project.

After creating the repository, you need to create an IAM user that will have access to the Code Commit service.

1. In the IAM service, create a new IAM user.
2. Assign appropriate permissions to the user to access the Code Commit service:
    - Service: CodeCommit
    - Access level:
    - List: All
    - Read: All
    - Write: All

3. Add a repository to restrict the user's access to a specific repository:
- ARN: Select the appropriate region and the repository name.
4. Create a policy name for the repository access policy.

Once the policy is created, go back to the IAM user and attach the policy to the user you created. Then create the user.

After creating the user, generate an SSH key and upload it to the IAM user's security credentials.

**To create an SSH key for the IAM user:**

1. Generate an SSH key from your local machine.
2. Copy the public key (_pub file) into the security credentials of the IAM user.Once the SSH key is created and added, you need to create an SSH config file that will be used when connecting to the AWS Code Commit host.

**To create the SSH config file:**

- Create a config file in the .ssh directory (e.g., vi config).
- Specify the host URL for the Code Commit repository.
Specify the user (the IAM user's username in the security credentials).
- Specify the identity file (the location of the SSH key's private key).
- Set the permissions of the config file to chmod 600.

To verify the SSH connection to the Code Commit repository:
- use the command **ssh git-commit.us-east-1.amazonaws.com** and add the -v option for detailed output.

**Cloning the Repository and Migrating Code**

To clone the Code Commit repository to your local machine and migrate the code from GitHub:

1. Clone the repository to the /tmp directory using the command git clone {{url}}.

To configure your local Git repository to push code to Code Commit:

- Switch to the branch you want to push code from (e.g., master branch).
- Run a command to extract the branches into a file, excluding the master and HEAD branches:
- git branch -a | grep -v HEAD | cut -d '/' -f3 | grep -v master > /tmp/branches

2. Automate the checkout of the extracted branches using a loop:
- for i in $(cat /tmp/branches); do echo $i; done

- Replace the echo $i command with git checkout $i to automatically checkout all the branches.

- If you need to fetch tags, use the command git fetch --tags.

3. Remove the remote origin:
- git remote rm origin
4. Add the Code Commit repository as the new remote origin:
- git remote add origin {{ssh-url-aws-code-commit}}
5. Verify the configuration by checking the .git/config file.
6. Push the code to the Code Commit repository:
- git push --all origin
- If you need to push tags as well, use git push --tags.

**Code Artifacts**

To create an artifact repository for the Maven build job:

    - Go to the Artifact service and create a repository.
    - Set the public upstream repository to "Maven" for Maven dependencies.
    - Select the appropriate domain:
    - AWS account: Depends on your use-case
    - Domain name: "vpro-app"

The artifact repository will contain two repositories: one for dependencies (Maven Central) and one for the group.

To connect the dependencies repository to the repository:

1. Follow the provided instructions.
2. Select "Maven" as the repository type.

Before using the generated token, create a user that will be configured for artifact access:

1. In the IAM service, create a new user.
2. Attach a policy for artifact access (optional).
3. Create an access key for the user and download the CSV file.

Configure AWS using the downloaded access key:

1. Run the command aws config and provide the necessary information.
2. Copy the Maven Central token for later use.
3. Update the settings.xml file and update the repository link in the pom.xml file.

Push the code to the artifact repository.

**SonarCloud**

To set up SonarCloud for code analysis:

- Go to the SonarCloud website.
- Create a security token in your profile's security settings and save it securely.
- Create a new project manually in SonarCloud:
- Project name: Provide a name.
    - Public or private: Choose public (since it's free).
- Save the project information for later use.
- Update the settings.xml file with the SonarCloud information.

**Use AWS Systems Manager Parameter Store to store variables:**

- Add parameters for each information saved during the SonarCloud project setup:
- Organization: {{name}}
- Host: {{name}}
- Project: {{name}}
- SonarToken: {{name}} (secure string)
- CodeArtifactToken: {{name}} (secure string)

**CodeBuild for Sonar Analysis**

To build the project for Sonar analysis:

1. Create a CodeBuild project.
2. Set the source as CodeCommit and provide the repository and branch information.
3. Configure the environment with managed image settings:
    - OS: Linux
    - Runtime: Standard
    - Image: 5.0
4. Create a service role for the project.
5. Set the build spec to use a custom build spec provided in the AWS files.
6. Configure CloudWatch settings:
    Group name: {{vpro-build-job}}
    Stream name: {{sonar-build-job}}

Before running the build, update the role to grant privileges to access SSM Parameter Store:

1. Go to CodeBuild, select the build project (e.g., vpro-ci-aws), and edit the environment.
2. Copy the role name.
3. Go to IAM, find the role with the specified name, and attach a policy.
4. Create a policy for System Manager with the following access levels:
    - List: DescribeParameter
    - Read: GetParameter, GetParameterByPath, GetParameterByHistory, GetParameters,DescribeDocumentsParameter

    - Resource: Specific to this account
    - Policy name: Enter a name for the policy.

5. Attach the created policy to the role.

Build the artifact after completing the setup. For quality gates, define conditions based on overall code metrics (e.g., bugs) and set them to fail the analysis if the conditions are met.

**CodeBuild for Artifact**

To build the artifact:

1. Follow the same steps as for Sonar analysis, but use a different build spec file.
2. Attach the System Manager policy created earlier to the build artifact role.

**CodePipeline and SNS Notification**

Before building the pipeline, set up SNS notifications:

    - Go to SNS and create a topic with a name.
    - Create a subscription for the topic, selecting the protocol (e.g., email) and providing the email address.
    - To create a CodePipeline:

    - Create a pipeline with a name.
    - Create a new service role.
    - Set the source provider as AWS CodeCommit and provide the repository and branch information.
    - Choose the detection method (e.g., CloudWatch Events).
    - Set the artifact format to CodePipeline.
    - Configure the build provider as AWS CodeBuild, selecting the region and the project name for artifact build.
    - Skip the deploy stage for now.

**Edit the pipeline:**

- Add a new stage between the source and build stages, named "Test."
- Set the action provider to AWS CodeBuild, region to us-east-1, and input artifact to the source.
- Set the project name to the project used for Sonar code analysis.

Add another stage after the build stage:

- Set the stage name to "Deploy."
- Create an action group named "Deploy to S3" with the action provider as S3 and input artifact as the build artifact.
- Before specifying the bucket name, create a bucket in S3 and create a folder inside it for the pipeline artifact.
- Set the bucket name and deployment path accordingly.

**Configure pipeline notifications:**

    - Go to the pipeline settings, navigate to notifications, and create a notification rule.
    - Provide a notification name and the event trigger.
    - Choose the target type as SNS and select the notification for the pipeline you created.