# Day 21 — SPEED TYPING (LEVEL 2)

You must rewrite all FOUR from scratch:

1. Ansible:
   - Install nginx
   - Start + enable service

2. Terraform:
   - Create EC2 (choose any HireReady-approved type)
   - Tag it with:
       Owner: HireReadyStudent

3. Kubernetes:
   - Deployment with 3 replicas
   - Image: nginx

4. Shell:
   - Print date, uptime, disk usage

⏱ TIME LIMIT: 15 minutes

# Multi-Environment Pipeline

## 🔹 Jenkins Pipeline — Deploy to Multiple Environments
You will learn how to **define multiple environment deployments in one pipeline**.

pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "HireReady"
    }
    stages {
        stage('Deploy to Dev') {
            steps {
                sh 'echo "Deploying $INSTITUTE_NAME to Dev environment"'
            }
        }
        stage('Deploy to Staging') {
            steps {
                input message: "Approve Staging Deployment?"
                sh 'echo "Deploying $INSTITUTE_NAME to Staging environment"'
            }
        }
        stage('Deploy to Prod') {
            steps {
                input message: "Approve Prod Deployment?"
                sh 'echo "Deploying $INSTITUTE_NAME to Prod environment"'
            }
        }
    }
}

## 🔹 GitLab CI — Multi-Environment
You will learn how to **define multiple environment deployments in GitLab CI**.

stages:
  - deploy

variables:
  INSTITUTE_NAME: "HireReady"

deploy-dev:
  stage: deploy
  image: alpine:latest
  script:
    - echo "Deploying $INSTITUTE_NAME to Dev environment"

deploy-staging:
  stage: deploy
  image: alpine:latest
  script:
    - echo "Deploying $INSTITUTE_NAME to Staging environment"
  when: manual

deploy-prod:
  stage: deploy
  image: alpine:latest
  script:
    - echo "Deploying $INSTITUTE_NAME to Prod environment"
  when: manual
