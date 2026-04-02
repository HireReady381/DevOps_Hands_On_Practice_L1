# Day 09 — Templates, Internet Gateway, ConfigMap, CPU Load

## 🔹 Ansible — Use Jinja2 Template

---
- name: Deploy template for HireReady
  hosts: all
  become: yes

  tasks:
    - name: Copy template file
      template:
        src: HireReady.conf.j2
        dest: /etc/HireReady.conf


## 🔹 Terraform — Internet Gateway

resource "aws_internet_gateway" "HireReadyIGW" {
  vpc_id = aws_vpc.HireReadyVPC.id

  tags = {
    Name = "HireReady-IGW"
  }
}


## 🔹 Kubernetes — Create ConfigMap

apiVersion: v1
kind: ConfigMap
metadata:
  name: HireReady-config
data:
  APP_MODE: "production"
  WELCOME_MSG: "Hello HireReady"


## 🔹 Shell Script — Check CPU Load

#!/bin/bash

LOAD=$(uptime | awk '{print $10}')

echo "Current CPU Load: $LOAD"


# Artifacts and Archiving

## 🔹 Jenkins Pipeline — Archive Artifacts
You will learn how to **save build artifacts for future use**.

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
        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
            }
        }
    }
}

## 🔹 GitLab CI — Artifacts
You will learn how to **store artifacts in GitLab CI**.

stages:
  - build

build:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - git clone https://github.com/HireReady/sample-java-app.git
    - cd sample-java-app
    - mvn clean package
  artifacts:
    paths:
      - target/*.jar
    expire_in: 1 week
