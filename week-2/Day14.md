# Day 14 — MINI PROJECT

Complete these steps:

## 1️⃣ Terraform — Create EC2 (any instance type)

- VPC
- Subnet
- Internet gateway
- Route table
- EC2 (c5.xlarge / r5.2xlarge / r6i.4xlarge / c6i.8xlarge / c6a.12xlarge)

## 2️⃣ Ansible — Install Nginx & Deploy HTML File

## 3️⃣ Kubernetes — Create Deployment + Service

## 4️⃣ Shell — Print CPU, Memory, Disk


# Scheduled Pipelines

## 🔹 Jenkins Pipeline — Scheduled Build
You will learn how to **schedule a Jenkins pipeline using cron**.

pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "HireReady"
    }
    triggers {
        cron('H 2 * * *') // every day at 2 AM
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/HireReady/sample-java-app.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
}

## 🔹 GitLab CI — Scheduled Pipeline
You will learn how to **schedule GitLab CI pipelines via cron**.

stages:
  - build

build:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - git clone https://github.com/HireReady/sample-java-app.git
    - cd sample-java-app
    - mvn clean package
