# AWS Cloud Static Website Deployment

This project hosts a static website on AWS using Docker, Amazon ECR, and ECS with Fargate. The project demonstrates how to containerize a static website, deploy it to AWS, and expose it via an Application Load Balancer (ALB) with SSL encryption.

## Overview

The project builds a container image from a Dockerfile and stores it in Docker Hub and Amazon ECR. It then deploys the container on AWS Fargate using an ECS cluster. Traffic is routed to the application through an Application Load Balancer, with DNS resolution managed by Route 53. The setup includes a VPC with public and private subnets spread across two Availability Zones to ensure high availability, fault tolerance, and logical separation of resources.

## Architecture Diagram

For a visual overview of the architecture, refer to the [reference diagram](jupiter-website/Static_Site_Docker_ECR_ECS.png). This diagram outlines:
- The VPC with public and private subnets across two Availability Zones.
- The Internet Gateway that connects the VPC to the internet.
- The Application Load Balancer (ALB) directing traffic to ECS tasks.
- The DNS setup in Route 53 pointing the domain name to the ALB.
- SSL termination at the ALB using an AWS Certificate Manager certificate.

## Project Components

### Docker & Containerization
- **Dockerfile:** Used to build the container image for the static website.
- **Docker Hub Repository:** Stores the created container image for version control and distribution.
- **Amazon ECR Repository:** Stores the container image in AWS for deployment.
  ```bash
  aws ecr create-repository --repository-name jupiter --region us-east-2
  ```

### AWS Infrastructure
- **VPC:** Provisioned with public and private subnets across two Availability Zones for high availability and fault tolerance. An Internet Gateway is attached to allow outbound internet access.
- **Security Groups:**
  - **ALB Security Group:** 
    - Allows HTTP (80) and HTTPS (443) traffic from 0.0.0.0/0.
  - **Container Security Group:** 
    - Allows HTTP (80) and HTTPS (443) traffic only from the ALB Security Group.
- **Application Load Balancer (ALB):** Routes incoming traffic to the ECS tasks based on target groups.
- **ECS & Fargate:** 
  - **Task Definition:** Defines container settings.
  - **ECS Service:** Manages Fargate containers running the static website.
- **Route 53:** 
  - A record set (A record) is created and points to the ALB, enabling users to access the website via a custom domain.
- **SSL/TLS:** 
  - An SSL certificate is registered with AWS Certificate Manager and applied to the ALB to secure communications between the browser and the web server.

## Setup and Deployment

Follow these steps to deploy the project:

1. **Build and Push the Docker Image:**
   - Build the container image using the provided Dockerfile.
   - Tag and push the image to Docker Hub for version control.
   - Create an Amazon ECR repository and push the container image to ECR:
     ```bash
     aws ecr create-repository --repository-name jupiter --region us-east-2
     # Follow up with docker login, tag, and push commands.
     ```

2. **Provision AWS Infrastructure:**
   - Create a VPC with public and private subnets across two availability zones.
   - Set up an Internet Gateway for external access.
   - Create two security groups:
     - **ALB Security Group:** Allow inbound traffic on ports 80 and 443.
     - **Container Security Group:** Allow inbound traffic on ports 80 and 443 only from the ALB Security Group.

3. **Configure the Application Load Balancer (ALB):**
   - Create an ALB and a target group.
   - Set up listeners on ports 80 and 443.
   - Register the ECS tasks as targets.

4. **Deploy ECS Tasks on Fargate:**
   - Define the task using the ECS Task Definition.
   - Create an ECS service that launches Fargate tasks from the task definition.

5. **DNS and SSL Setup:**
   - Create a record set in Route 53 pointing your custom domain to the ALB.
   - Register for an SSL certificate in AWS Certificate Manager.
   - Attach the SSL certificate to the ALB to ensure encrypted communication.

## References

- [Docker Documentation](https://docs.docker.com)
- [AWS ECS and Fargate Documentation](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/AWS_Fargate.html)
- [AWS ECR Documentation](https://docs.aws.amazon.com/AmazonECR/latest/userguide/what-is-ecr.html)
- [AWS Route 53 Documentation](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/Welcome.html)
- [AWS Certificate Manager Documentation](https://docs.aws.amazon.com/acm/latest/userguide/)
