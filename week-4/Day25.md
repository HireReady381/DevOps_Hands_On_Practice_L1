# Day 25 — Lineinfile, Key Pair, Multi-Container, File Count

## 🔹 Ansible — Modify Existing Line

---
- name: Update SSH config for HireReady
  hosts: all
  become: yes

  tasks:
    - name: Disable root login
      lineinfile:
        path: /etc/ssh/sshd_config
        regexp: "^PermitRootLogin"
        line: "PermitRootLogin no"


## 🔹 Terraform — EC2 Key Pair Output

resource "aws_key_pair" "HireReadyKey" {
  key_name   = "HireReadyKey"
  public_key = file("~/.ssh/id_rsa.pub")
}

output "HireReady_Key_Name" {
  value = aws_key_pair.HireReadyKey.key_name
}


## 🔹 Kubernetes — Multi-Container Pod (Sidecar Logging)

apiVersion: v1
kind: Pod
metadata:
  name: HireReady-sidecar
spec:
  containers:
    - name: app
      image: nginx
    - name: log-writer
      image: busybox
      command: ["sh", "-c", "while true; do echo log entry; sleep 5; done"]


## 🔹 Shell Script — Count Files in Directory

#!/bin/bash

DIR="/var/log"

COUNT=$(ls $DIR | wc -l)

echo "Total files in $DIR: $COUNT"


# Advanced Caching

## 🔹 Jenkins Pipeline — Cache Dependencies
You will learn how to **cache Maven dependencies with realistic labels**.

pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "HireReady"
        TEAM = "DevOps"
        ENV = "staging"
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/HireReady/sample-java-app.git'
            }
        }
        stage('Build') {
            steps {
                sh '''
                if [ -d ".m2" ]; then
                    echo "Using cached dependencies for $INSTITUTE_NAME in $ENV by $TEAM"
                else
                    mvn clean compile
                fi
                '''
            }
        }
    }
}

## 🔹 GitLab CI — Cache Dependencies
You will learn how to **cache dependencies in GitLab CI with tags**.

stages:
  - build

variables:
  INSTITUTE_NAME: "HireReady"
  TEAM: "DevOps"
  ENV: "staging"

build:
  stage: build
  image: maven:3.8.1-jdk-17
  cache:
    key: "${ENV}-maven-cache"
    paths:
      - .m2/repository
  script:
    - mvn clean compile
    - echo "Cached dependencies for $INSTITUTE_NAME in $ENV by $TEAM"
