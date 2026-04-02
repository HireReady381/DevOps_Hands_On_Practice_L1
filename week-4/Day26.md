# Day 26 — Packages + Services, R53 Record, InitContainer, Status Script

## 🔹 Ansible — Install + Start Multiple Services

---
- name: Install and enable services
  hosts: all
  become: yes

  tasks:
    - name: Install services
      yum:
        name:
          - httpd
          - firewalld
        state: present

    - name: Enable and start services
      service:
        name: "{{ item }}"
        state: started
        enabled: yes
      loop:
        - httpd
        - firewalld


## 🔹 Terraform — Route53 Record

resource "aws_route53_record" "HireReadyRecord" {
  zone_id = "Z1234567890"
  name    = "app.HireReady.com"
  type    = "A"
  ttl     = 300
  records = [aws_eip.HireReadyEIP.public_ip]
}


## 🔹 Kubernetes — Init Container

apiVersion: v1
kind: Pod
metadata:
  name: HireReady-init
spec:
  initContainers:
    - name: setup
      image: busybox
      command: ["sh", "-c", "echo Initializing; sleep 10"]
  containers:
    - name: main
      image: nginx


## 🔹 Shell Script — System Status

#!/bin/bash

echo "Hostname: $(hostname)"
echo "Uptime: $(uptime)"
echo "IP: $(hostname -I)"


# GitLab Dynamic Environments

## 🔹 Jenkins Pipeline — Deploy to Dynamic Environment
You will learn how to **simulate deployment to dynamic environments with tags**.

pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "HireReady"
        TEAM = "DevOps"
    }
    parameters {
        string(name: 'ENVIRONMENT', defaultValue: 'dev', description: 'Deployment Environment')
    }
    stages {
        stage('Deploy') {
            steps {
                sh 'echo "Deploying $INSTITUTE_NAME app to $ENVIRONMENT for $TEAM team"'
            }
        }
    }
}

## 🔹 GitLab CI — Dynamic Environment Deployment
You will learn how to **use GitLab dynamic environment names with realistic tags**.

stages:
  - deploy

variables:
  INSTITUTE_NAME: "HireReady"
  TEAM: "DevOps"

deploy:
  stage: deploy
  image: alpine:latest
  environment:
    name: $CI_COMMIT_REF_NAME
    url: http://$CI_COMMIT_REF_NAME.HireReady.com
  script:
    - echo "Deploying $INSTITUTE_NAME app to $CI_COMMIT_REF_NAME for $TEAM team"
