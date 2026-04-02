# Day 11 — Database, Outputs, Sidecars, Functions

## 🔹 Ansible — Install MariaDB

---
- name: Install MariaDB for HireReady
  hosts: all
  become: yes

  tasks:
    - name: Install MariaDB
      yum:
        name: mariadb-server
        state: present

    - name: Start MariaDB
      service:
        name: mariadb
        state: started
        enabled: yes


## 🔹 Terraform — Output Public IP (r5.2xlarge)

resource "aws_instance" "HireReadyServer" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "r5.2xlarge"

  tags = {
    Name = "HireReady-Output-EC2"
  }
}

output "HireReadyPublicIP" {
  value = aws_instance.HireReadyServer.public_ip
}


## 🔹 Kubernetes — Sidecar Container

apiVersion: v1
kind: Pod
metadata:
  name: HireReady-sidecar
spec:
  containers:
    - name: main
      image: nginx
    - name: sidecar
      image: busybox
      command: ["sh", "-c", "echo Sidecar running; sleep 3600"]


## 🔹 Shell Script — Function Example

#!/bin/bash

greet() {
  echo "Welcome to HireReady DevOps Training"
}

greet



# Notifications

## 🔹 Jenkins Pipeline — Email Notifications
You will learn how to **send email notifications on failure or success**.

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
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
    }
    post {
        success {
            mail to: 'team@HireReady.com',
                 subject: "SUCCESS: Build #${env.BUILD_NUMBER}",
                 body: "Build completed successfully for $INSTITUTE_NAME"
        }
        failure {
            mail to: 'team@HireReady.com',
                 subject: "FAILURE: Build #${env.BUILD_NUMBER}",
                 body: "Build failed for $INSTITUTE_NAME"
        }
    }
}

## 🔹 GitLab CI — Notifications
You will learn how to **notify via email after job completion**.

stages:
  - build

build:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - git clone https://github.com/HireReady/sample-java-app.git
    - cd sample-java-app
    - mvn clean package
  after_script:
    - echo "Sending notification email to team@HireReady.com"
