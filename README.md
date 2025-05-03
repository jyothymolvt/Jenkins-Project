# Jenkins-Project
Dockerized PHP App Deployment via Jenkins Pipeline (ECS + ECR + AWS)

This project automates the containerization, versioning, and deployment of a sample PHP application using a Jenkins CI/CD pipeline integrated with AWS services.
🔧 Tech Stack:

    Jenkins – CI/CD Orchestration

    Docker – Containerization

    AWS ECR – Docker image registry

    AWS ECS – Container orchestration

    Boto3 (Python) – AWS SDK for ECS automation

    Shell Scripts – For file generation and Docker commands

    PHP (Apache) – Simple web application

📋 Pipeline Stages:

    Stage 1: Dynamically generate a simple index.php file

    Stage 2: Create a Dockerfile for the PHP app using the official PHP-Apache base image

    Stage 3: Extract tag version from the GitLab pipeline for image versioning

    Stage 4: Build, tag, and push the Docker image to AWS ECR

    Stage 5: Register a new ECS task definition with the updated Docker image using boto3

    Stage 6: Update the ECS service to use the new task definition and deploy the updated container
