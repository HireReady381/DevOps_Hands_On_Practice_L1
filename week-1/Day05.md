# Day 05 — Handlers, User-Data, Resource Limits

## 🔹 Ansible — Handler to Restart Nginx

---
- name: Manage Nginx
  hosts: all
  become: yes

  tasks:
    - name: Update config file
      copy:
        src: index.html
        dest: /usr/share/nginx/html/index.html
      notify: Restart Nginx

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted


## 🔹 Terraform — EC2 with User Data (c6a.12xlarge)

resource "aws_instance" "HireReadyEC2" {
  ami           = "ami-0abcd1234abcd1234"
  instance_type = "c6a.12xlarge"

  user_data = <<EOF
#!/bin/bash
yum install -y httpd
systemctl start httpd
EOF

  tags = {
    Name = "HireReady-EC2"
  }
}


## 🔹 Kubernetes — Resource Limits

apiVersion: v1
kind: Pod
metadata:
  name: HireReady-limited-pod
spec:
  containers:
    - name: app
      image: nginx
      resources:
        limits:
          memory: "256Mi"
          cpu: "500m"


# Day 05 — Environment Variables

## 🔹 Jenkins Pipeline — Use Environment Variables
You will learn how to **use environment variables in Jenkins pipelines**.

pipeline {
    agent any
    environment {
        INSTITUTE_NAME = "HireReady"
    }
    stages {
        stage('Print') {
            steps {
                sh 'echo "Welcome to $INSTITUTE_NAME DevOps Training"'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package -Dinstitute.name=$INSTITUTE_NAME'
            }
        }
    }
}

## 🔹 GitLab CI — Use Environment Variables
You will learn how to **use CI variables in GitLab pipelines**.

stages:
  - build

variables:
  INSTITUTE_NAME: "HireReady"

build:
  stage: build
  image: maven:3.8.1-jdk-17
  script:
    - git clone https://github.com/HireReady/sample-java-app.git
    - cd sample-java-app
    - echo "Welcome to $INSTITUTE_NAME DevOps Training"
    - mvn clean package -Dinstitute.name=$INSTITUTE_NAME
