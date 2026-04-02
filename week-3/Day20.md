# Day 20 — DEBUG DAY

Fix these intentionally broken files:

## 🔹 Ansible — Broken

- name Install stuff
  hosts all
  tasks:
   -name: install
    yum:
      name httpd
      state present


## 🔹 Terraform — Broken

resource aws_instance "Bad" {
 ami "ami-123"
 instance_type c6i.8xlarge
}


## 🔹 Kubernetes — Broken

apiVersion apps/v1
kind Deployment
metadata:
 name broken-app
spec:
replicas:3
selector:
 matchLabels:
   app: broken
template:
 metadata:
  labels:
   app: broken
 spec:
  containers:
   -name: app
    image nginx


# Secrets & Credentials

## 🔹 Jenkins Pipeline — Using Credentials
You will learn how to **use credentials securely in Jenkins**.

pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "HireReady"
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/HireReady/sample-java-app.git'
            }
        }
        stage('Use Secret') {
            steps {
                withCredentials([string(credentialsId: 'HireReady_API_KEY', variable: 'API_KEY')]) {
                    sh 'echo "Using secret $API_KEY for $INSTITUTE_NAME"'
                }
            }
        }
    }
}

## 🔹 GitLab CI — Using CI/CD Variables
You will learn how to **use GitLab CI variables securely**.

stages:
  - build

variables:
  INSTITUTE_NAME: "HireReady"
  API_KEY: "PLACEHOLDER_SECRET"

build:
  stage: build
  image: alpine:latest
  script:
    - echo "Using secret $API_KEY for $INSTITUTE_NAME"
