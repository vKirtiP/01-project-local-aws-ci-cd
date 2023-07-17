
##  Continuous Integration Using Jenkins, Nexus, Sonarqube & Slack

The project aims to achieve continuous integration by implementing a pipeline using Jenkins, Nexus,SonarQube, and Slack, enabling automated builds, testing, and notifications for each commit, with automated code fixing for identified bugs or errors.






##  Project Description

The project focuses on implementing a robust continuous integration system utilizing Jenkins, Nexus, SonarQube, and Slack.

The primary objective is to establish an automated workflow that seamlessly builds and tests the codebase for every commit, ensuring code quality and identifying any bugs or errors. 

The system further integrates with Slack to provide real-time notifications about the build status, allowing the development team to stay informed and act promptly. 

In addition, SonarQube is utilized to perform code analysis and provide actionable insights for improving code quality. 

By adopting this automated system, the project aims to enhance development efficiency, reduce manual efforts, and deliver high-quality software with minimized errors.
## Architecture

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/ci-jenkins/Step_SS/arch_2.png)

![App Screenshot](https://github.com/vKirtiP/project-local-aws-ci-cd/blob/ci-jenkins/Step_SS/arch_1.png)

## Deployment

**Security Group and Keypair**

We create security groups and key pairs for SonarQube, Nexus, and Jenkins.

Jenkins:

    - SSH
        - 8080 (My IPv4)
        - 8080 (Anywhere IPv6)
        - 80 (SonarQube Security Group)
Nexus:

    - SSH
        - 8081 (IPv4)
        - 8081 (Jenkins Security Group)

SonarQube:

    - SSH
        - 80 (My IP)
        - 80 (Jenkins Security Group)

Step 2: Launching EC2 Instances
    - Install Jenkins (t2.small)
    - Install SonarQube (t2.medium)
    - Install Nexus (t2.medium)

Step 3: Post Installation

Jenkins:

    - Install necessary plugins:
        - Maven Integration
        - SonarQube Scanner
        - Nexus Artifact Uploader
        - GitHub Integration
        - Build Timestamp (for artifact versioning)
        - Slack Notification

Nexus:

    - Set up 4 repositories (Maven host, Maven proxy, Maven snapshot, Maven group) to manage dependencies and uploads.

SonarQube:

    - Check systemctl and verify login credentials (admin/admin).

**GitHub Migration:**

    - Create a private repository for the project (ci-jenkins branch).
    - Generate an SSH key and add it to GitHub.
    - Verify SSH connection to the GitHub repository.
    - Clone the project directory to a local location.
    - Rename the project as desired.
    - Update the repository URL in the .git/config file.
    - Create a new main branch and switch to the ci-jenkins branch.
    - Push all the code to the private repository.

**Build Job with Nexus:**

    - Install Java and Maven in Jenkins.
    - Create login credentials for Nexus.
    - Create a Jenkins pipeline script and push it to GitHub.
    - Create a new pipeline in Jenkins.
    - Set the pipeline script from SCM (URL of the repository).
    - Configure Jenkins SSH access for GitHub.
    - Set the branch name as "ci-jenkins".
    - Set the script path to "Jenkinsfile".
    - Build the pipeline.

**Webhooks:**

    - Set up webhooks in the repository settings to automatically trigger Jenkins job builds on GitHub push events.
    - Ensure the Jenkins server's IP address is updated in the webhook settings.

**Code Analysis with SonarQube:**

    - Export reports from unit testing and checkstyle analysis in the target folder.
    - Utilize the SonarScanner tool for code analysis.
    - Configure SonarQube server information in Jenkins.
    - Set up global configuration for SonarQube scanner in Jenkins, including the token for authentication.

**Sonar Quality Gates:**

    - Create a new quality gate with specific conditions.
    - Associate the quality gate with the project.
    - Set up a webhook to trigger the quality gate on Jenkins.
    - Publish Artifact to Nexus Repository:

    - Update the Jenkins pipeline script to publish artifacts to the Nexus repository.

    - Adjust the build timestamp in the Jenkins configuration.
    - Push the code to trigger the pipeline.

**Slack Notification:**

    - Create a new workspace in Slack.
    - Add Jenkins CI to the Slack workspace, choosing the desired channel.
    - Copy the token ID.
    - Configure the Slack notification in Jenkins, specifying the workspace and adding the secret key.

This sequence of steps establishes a continuous integration system using Jenkins, Nexus, SonarQube, and Slack.

It automates the build and testing processes for each commit, identifies and fixes code issues, and provides real-time notifications through Slack integration.