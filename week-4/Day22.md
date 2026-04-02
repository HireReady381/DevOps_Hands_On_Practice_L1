# Day 22 — Handlers, Security Group, DaemonSet, Disk Monitor

## 🔹 Ansible — Use Handler on Config Change

---
- name: Configure Nginx for HireReady
  hosts: all
  become: yes

  tasks:
    - name: Copy Nginx config
      copy:
        dest: /etc/nginx/nginx.conf
        content: |
          user nginx;
          worker_processes auto;
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      service:
        name: nginx
        state: restarted


## 🔹 Terraform — Security Group

resource "aws_security_group" "HireReadySG" {
  name   = "HireReady-sg"
  vpc_id = aws_vpc.HireReadyVPC.id

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  tags = {
    Name = "HireReady-SG"
  }
}


## 🔹 Kubernetes — DaemonSet

apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: HireReady-daemon
spec:
  selector:
    matchLabels:
      app: ds
  template:
    metadata:
      labels:
        app: ds
    spec:
      containers:
        - name: monitor
          image: busybox
          command: ["sh", "-c", "while true; do echo HireReady Node Monitor; sleep 20; done"]


## 🔹 Shell Script — Monitor Disk Usage

#!/bin/bash

USAGE=$(df -h / | tail -1 | awk '{print $5}')

echo "Disk Usage: $USAGE"


# Advanced Triggers

## 🔹 Jenkins Pipeline — Trigger on Git Tag
You will learn how to **trigger builds on Git tags** with realistic tags.

pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "HireReady"
        TEAM = "DevOps"
        ENV = "prod"
    }
    triggers {
        pollSCM('H/15 * * * *')
    }
    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/HireReady/sample-java-app.git', branch: 'main'
            }
        }
        stage('Build') {
            when {
                buildingTag()
            }
            steps {
                sh 'echo "Building $INSTITUTE_NAME app for $TEAM team in $ENV environment"'
            }
        }
    }
}

## 🔹 GitLab CI — Trigger on Tag
You will learn how to **trigger CI jobs when a tag is pushed**.

stages:
  - build

variables:
  INSTITUTE_NAME: "HireReady"
  TEAM: "DevOps"
  ENV: "prod"

build:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - echo "Building $INSTITUTE_NAME app for $TEAM team in $ENV environment"
  only:
    - tags
