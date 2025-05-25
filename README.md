🧱 Infrastructure Overview

Dev Node: EC2 (Spot) – t2.medium, 50GB gp3

Pre-Live Node: EC2 (Spot) – t2.medium, 70GB gp3

Prod Node: EC2 (On-Demand) – m5.large, 50GB io1

All servers use Ubuntu 22.04 LTS.

📁 Directory Structure

jenkins-infra-setup/
├── jenkins/                     # Jenkins configuration
├── ansible/                     # Ansible playbooks for setup
├── docker/                      # Dockerfiles
├── packer/                      # Packer templates
├── terraform/                   # Infrastructure-as-code
├── maven/                       # Maven settings
├── ssl/                         # SSL certificate setup
├── install-scripts/             # Shell scripts for tool installation
└── docs/                        # Documentation

🚀 Step-by-Step Setup

1. ✅ EC2 Instance Creation

Use Terraform to define and provision the 3 EC2 nodes.

Example AMI: ubuntu/images/hvm-ssd/ubuntu-jammy-22.04-amd64-server

cd terraform/
terraform init
terraform plan
terraform apply

2. 🔧 Install Required Tools (on all nodes)

Use Ansible or manual install via install-scripts/install-tools.sh:

Java 17

Maven 

Docker & Docker Compose

Terraform

Ansible

Packer

Trivy

AWS CLI / Azure CLI

3. 🧩 Jenkins Installation (Master Node)

wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt update && sudo apt install jenkins

Start and enable Jenkins:

sudo systemctl start jenkins
sudo systemctl enable jenkins

4. 👷 Configure Jenkins Slave Nodes

On each slave node:

Install Java

Create Jenkins user

Add SSH key to Jenkins master

From Jenkins UI:

Manage Jenkins > Nodes > New Node > Permanent Agent

Configure SSH credentials and remote root directory

5. 🔒 SSL Setup with Let's Encrypt

Using certbot:

sudo apt install certbot
sudo certbot certonly --standalone -d yourdomain.com

Set up renewal cron job:

0 3 * * * certbot renew --quiet

6. 📊 SonarQube Setup

Launch a separate EC2 or Docker container

Configure MySQL/PostgreSQL as DB

In Jenkins: Install SonarQube Scanner plugin

SonarQube sample configuration in Jenkins:

SonarQube servers > Add > URL, auth token

Pipeline usage:

withSonarQubeEnv('My SonarQube') {
    sh 'mvn sonar:sonar'
}

7. 🔁 Sample Jenkins Pipeline

pipeline {
    agent none
    stages {
        stage('Dev Tools Verification') {
            when { branch 'development' }
            agent { label 'DEV' }
            steps {
                sh 'mvn --version'
                sh 'java --version'
                sh 'terraform --version'
                sh 'packer --version'
                sh 'trivy --version'
            }
        }
    }
}

🔐 Security Best Practices

Use strong SSH keys

Enable MFA on AWS account

Regularly update Jenkins and plugins

Use secrets manager for credentials

📦 Useful Commands

Jenkins CLI:

java -jar jenkins-cli.jar -s http://localhost:8080/ help

Restart Jenkins:

sudo systemctl restart jenkins

🧹 Cleanup

To destroy infrastructure:

cd terraform/
terraform destroy

📘 References

Jenkins: https://www.jenkins.io/

SonarQube: https://docs.sonarqube.org/

Terraform AWS Provider: https://registry.terraform.io/providers/hashicorp/aws/latest/docs

