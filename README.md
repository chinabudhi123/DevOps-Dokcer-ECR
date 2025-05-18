# Apache HTTPD Docker Deployment Pipeline with Jenkins and AWS ECR

This repository contains the Dockerized Apache HTTPD application along with a Jenkins pipeline to build, push the Docker image to AWS Elastic Container Registry (ECR), and deploy it to an AWS EC2 instance.

---

## Repository Contents

- **Dockerfile**: Defines the Apache HTTPD base image and copies a simple `index.html` for the web server.
- **index.html**: A simple web page served by the Apache HTTPD container.
- **Jenkinsfile**: Defines the Jenkins pipeline to build, push, and deploy the Docker image.
- **README.md**: This documentation file.

---

## Prerequisites

- AWS account with ECR repository created.
- Jenkins server with:
  - Docker installed
  - AWS CLI installed and configured with appropriate permissions
  - SSH credentials for EC2 added as Jenkins credential (`ec2-access` in this case)
- EC2 instance with Docker installed and running, accessible via SSH.

---

## Jenkins Pipeline Overview

The pipeline performs the following steps:

1. **Checkout from GitHub**: Pulls the source code from this repository.
2. **Build Docker Image**: Builds a Docker image using the `Dockerfile` in this repo.
3. **Login to AWS ECR**: Authenticates Docker to your AWS ECR repository.
4. **Push to ECR**: Pushes the Docker image to your ECR repository.
5. **Manual Approval**: Waits for manual confirmation before deploying.
6. **Deploy to EC2**: Connects via SSH to the EC2 instance, pulls the latest image from ECR, and runs the container on port 80.

---

## How to Use

1. Clone this repository:

    ```bash
    git clone https://github.com/chinabudhi123/DevOps-Dokcer-ECR.git
    cd DevOps-Dokcer-ECR
    ```

2. Configure Jenkins pipeline:

    - Add the repository URL in your Jenkins pipeline.
    - Make sure Jenkins credentials are set for:
      - SSH key to EC2 instance (`ec2-access`)
      - AWS credentials with permissions to push/pull from ECR (configured on Jenkins or via AWS CLI on the agent)
    - Ensure Docker and AWS CLI are installed on the Jenkins build agent.

3. Run the Jenkins pipeline:

    - The pipeline will build the Apache HTTPD image.
    - Push it to your ECR repo.
    - Wait for manual approval.
    - Deploy the container to the EC2 instance.

4. Access the deployed application by navigating to:

    ```
    http://<EC2-instance-public-ip>/
    ```

---

## Dockerfile Summary

- Uses the official `httpd` Apache base image.
- Copies `index.html` to the Apache default document root.

---

## Troubleshooting

- Make sure the EC2 instance’s security group allows inbound traffic on port 80.
- Ensure Jenkins can SSH into the EC2 instance without password prompts.
- Verify AWS CLI on Jenkins agent has permission to interact with ECR.
- Check Docker service is running on EC2 before deployment.

---

## Contact

For any issues or suggestions, please open an issue or contact the repository owner.

---

*This setup is ideal for simple Apache HTTPD web apps and serves as a template to extend for other containerized apps.*


