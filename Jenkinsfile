pipeline {
    agent any

    stages {
        stage('stage-1') {
            steps {
                   sh '''#!/bin/bash
cat <<EOF > index.php
<?php
echo "<h1>Welcome to My Sample PHP Page!</h1>";
echo "<p>This is a sample Dockerized PHP application.</p>";
EOF
echo "Indexfile created:"
cat index.php'''
                    
            }
        }
        stage('stage-2')  {
            steps {
                    sh '''#!/bin/bash
cat <<EOF > Dockerfile
# Use the official PHP image with Apache
FROM php:8.1-apache

# Copy the PHP file to the Apache web directory
COPY index.php /var/www/html/

# Expose port 80
EXPOSE 80

# Start Apache in the foreground
CMD ["apache2-foreground"]
EOF
echo "Dockerfile created:"
ls -la
cat Dockerfile
'''
            }
        }

stage('stage-3')  {
            steps {
                script {
                   def gitlabBranch = env.gitlabBranch
def version = null
def match = gitlabBranch =~ /refs\/tags\/(.+)/
if (match) {
  version = match[0][1]
          }
echo "Extracted version: ${version}"

env.TAG = version 
                    
                }
            }
        } 
           

stage('stage-4')  {
     steps {
            sh '''
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin 503561451240.dkr.ecr.ap-south-1.amazonaws.com


docker build -t clado-pipjen:$TAG .


docker tag clado-pipjen:$TAG 503561451240.dkr.ecr.ap-south-1.amazonaws.com/clado-pipjen:$TAG

docker push 503561451240.dkr.ecr.ap-south-1.amazonaws.com/clado-pipjen:$TAG
'''

            }
        }   
     stage('stage-5')  {
     steps {
            sh '''#!/var/lib/jenkins/clado-env/bin/python3
import boto3
import os

tag = os.getenv("TAG")

# Initialize the ECS client
ecs_client = boto3.client('ecs', region_name='ap-south-1')

# Fetch the existing task definition
response = ecs_client.describe_task_definition(taskDefinition='clado-vpc2-td')

# Update the image in container definitions
container_definitions = response['taskDefinition']['containerDefinitions']
container_definitions[0]['image'] = '503561451240.dkr.ecr.ap-south-1.amazonaws.com/clado-pipjen:'+tag

# Re-register the task definition
ecs_td_res = ecs_client.register_task_definition(
    family=response['taskDefinition']['family'],  # Use the same family
    containerDefinitions=container_definitions,  # Updated container definitions
    networkMode='bridge',
    executionRoleArn='arn:aws:iam::503561451240:role/ecsTaskExecutionRole',
    cpu=response['taskDefinition'].get('cpu'),
    memory=response['taskDefinition'].get('memory')
)

# Output the result
print("New Task Definition Registered:")
print(ecs_td_res)

'''
     }
    }

    stage('stage-6')  {
     steps {
            sh '''#!/var/lib/jenkins/clado-env/bin/python3
import boto3

ecs_client = boto3.client('ecs', region_name='ap-south-1')

response = ecs_client.describe_task_definition(taskDefinition='clado-vpc2-td')

new_response = response['taskDefinition']['taskDefinitionArn']

resp = ecs_client.update_service(
 cluster='clado-vpc2-clus',
 service='clado-vpc2-service',
 taskDefinition=new_response 
)
'''
}
}
    }
}
