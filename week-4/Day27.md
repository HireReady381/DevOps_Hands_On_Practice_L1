# Day 27 — Copy Directory, EBS Volume, NodePort, Log Cleaner

## 🔹 Ansible — Copy Directory Recursively

---
- name: Copy HireReady files
  hosts: all
  become: yes

  tasks:
    - name: Copy directory
      copy:
        src: /opt/HireReady/
        dest: /backup/HireReady/
        remote_src: yes


## 🔹 Terraform — EBS Volume + Attachment

resource "aws_ebs_volume" "HireReadyVolume" {
  availability_zone = "us-east-1a"
  size              = 20

  tags = {
    Name = "HireReady-Volume"
  }
}

resource "aws_volume_attachment" "HireReadyAttach" {
  device_name = "/dev/sdh"
  volume_id   = aws_ebs_volume.HireReadyVolume.id
  instance_id = aws_instance.HireReadyEC2.id
}


## 🔹 Kubernetes — NodePort Service

apiVersion: v1
kind: Service
metadata:
  name: HireReady-nodeport
spec:
  type: NodePort
  selector:
    app: HireReady
  ports:
    - port: 80
      targetPort: 80
      nodePort: 30080


## 🔹 Shell Script — Clean Old Logs

#!/bin/bash

find /var/log -type f -mtime +7 -exec rm -f {} \;

echo "Old logs removed."


# Jenkins Scripted Pipeline

## 🔹 Jenkins Scripted Pipeline — Complex Logic
You will learn how to **write scripted pipelines with conditional logic**.

node {
    def envName = "dev"
    env.INSTITUTE_NAME = "HireReady"
    env.TEAM = "DevOps"

    stage('Checkout') {
        git url: 'https://github.com/HireReady/sample-java-app.git'
    }

    stage('Build') {
        if (envName == "dev") {
            sh 'echo "Building $INSTITUTE_NAME app in dev environment for $TEAM"'
        } else {
            sh 'echo "Skipping build for $envName"'
        }
    }

    stage('Deploy') {
        input message: "Approve deployment for $envName?"
        sh 'echo "Deploying $INSTITUTE_NAME app to $envName for $TEAM team"'
    }
}
