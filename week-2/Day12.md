# Day 12 — File Content, RDS Skeleton, Rolling Update, Backup

## 🔹 Ansible — Ensure File With Content

---
- name: Create config file for HireReady
  hosts: all
  become: yes

  tasks:
    - name: Write content to file
      copy:
        dest: /etc/HireReady.conf
        content: |
          Welcome to HireReady DevOps Training
          Today is Day 12


## 🔹 Terraform — RDS Skeleton

resource "aws_db_instance" "HireReadyRDS" {
  allocated_storage      = 20
  engine                 = "mysql"
  instance_class         = "db.t3.micro"
  name                   = "HireReadydb"
  username               = "admin"
  password               = "password123"
  skip_final_snapshot    = true
}


## 🔹 Kubernetes — Rolling Update Strategy

apiVersion: apps/v1
kind: Deployment
metadata:
  name: HireReady-rolling
spec:
  replicas: 3
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
  selector:
    matchLabels:
      app: roll
  template:
    metadata:
      labels:
        app: roll
    spec:
      containers:
        - name: web
          image: nginx


## 🔹 Shell Script — Backup Directory

#!/bin/bash

SOURCE="/var/log"
DEST="/backup/HireReady-$(date +%F).tar.gz"

tar -czf $DEST $SOURCE
echo "Backup created at: $DEST"


# Multi-branch Pipelines

## 🔹 Jenkins Pipeline — Multi-branch
You will learn how to **handle multiple branches in Jenkins pipelines**.

pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "HireReady"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }
    }
}

## 🔹 GitLab CI — Branch Specific Jobs
You will learn how to **run jobs for specific branches in GitLab CI**.

stages:
  - build

build-main:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - mvn clean compile
  only:
    - main

build-dev:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - mvn clean compile
  only:
    - dev
