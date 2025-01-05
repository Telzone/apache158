Hosting Apache Website using Jenkins
This repository contains configuration and setup instructions for hosting an Apache website with automated deployment using Jenkins on Amazon Linux 2.
Prerequisites

Amazon Linux 2 EC2 instance
Root or sudo access
Port 8080 open for Jenkins
Port 80 open for Apache
GitHub account

Installation Steps
1. Apache/Httpd Server Setup
First, install and configure the Apache web server:
bashCopysudo su
sudo yum update
sudo yum install httpd -y
sudo systemctl start httpd
sudo systemctl status httpd
2. Website Configuration
Create a basic website in the Apache web root:
bashCopycd /var/www/html
nano index.html
3. Git Setup
Install and configure Git:
bashCopyyum install git -y
git config --global user.name "your name"
git config --global user.email "your email@gmail.com"
4. Jenkins Installation
Install Jenkins and its dependencies:
bashCopy# Add Jenkins repository
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io-2023.key

# Install Java
sudo amazon-linux-extras enable java-openjdk11
sudo yum install java-17-amazon-corretto-devel -y

# Install Jenkins
sudo yum upgrade -y
sudo yum install jenkins -y

# Configure Java
export JAVA_HOME=/usr/lib/jvm/java-17-amazon-corretto.x86_64
source ~/.bash_profile

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
5. Jenkins Configuration

Access Jenkins at http://<your-ec2-instance-public-ip>:8080
Retrieve initial admin password:
bashCopysudo cat /var/lib/jenkins/secrets/initialAdminPassword

Install suggested plugins
Create admin user
Install additional required plugins:

GitHub Integration
Pipeline
Git



6. GitHub Webhook Setup

In your GitHub repository:

Go to Settings > Webhooks
Add webhook:

Payload URL: http://<your-ec2-instance-public-ip>:8080/github-webhook/
Content Type: application/json





7. Jenkins Pipeline Setup

Create new Pipeline job
Enable "GitHub hook trigger for GITScm polling"
Add the provided pipeline script to your job configuration

8. System Configuration
Configure sudo permissions for Jenkins:
bashCopysudo visudo
Add the following permissions:
Copyjenkins ALL=(ALL) NOPASSWD: /usr/bin/mkdir -p /var/www/html
jenkins ALL=(ALL) NOPASSWD: /usr/bin/rm -rf /var/www/html/*
jenkins ALL=(ALL) NOPASSWD: /usr/bin/cp -r * /var/www/html/
jenkins ALL=(ALL) NOPASSWD: /bin/chown -R apache apache /var/www/html
jenkins ALL=(ALL) NOPASSWD: /bin/chmod -R 755 /var/www/html
jenkins ALL=(ALL) NOPASSWD: /usr/sbin/nginx
jenkins ALL=(ALL) NOPASSWD: /bin/systemctl start httpd
jenkins ALL=(ALL) NOPASSWD: /bin/systemctl stop httpd
jenkins ALL=(ALL) NOPASSWD: /bin/systemctl reload httpd
jenkins ALL=(ALL) NOPASSWD: /bin/systemctl status httpd
jenkins ALL=(ALL) NOPASSWD: /bin/mkdir
jenkins ALL=(ALL) NOPASSWD: /bin/cp -r
jenkins ALL=(ALL) NOPASSWD: /bin/rm -rf
jenkins ALL=(ALL) NOPASSWD: /bin/chown -R
jenkins ALL=(ALL) NOPASSWD: /bin/chmod -R
jenkins ALL=(ALL) NOPASSWD: ALL
Pipeline Features
The included Jenkins pipeline:

Performs workspace cleanup
Clones the repository
Creates backups of current deployment
Deploys to Apache
Verifies deployment
Includes automatic rollback on failure

Troubleshooting

If Jenkins fails to start, check Java installation:
bashCopyjava -version

If Apache is not accessible, verify service status:
bashCopysudo systemctl status httpd

For permission issues, verify Jenkins user permissions:
bashCopyls -l /var/www/html


Security Notes

Ensure proper security group configuration in AWS
Regularly update security permissions
Use HTTPS for production environments
Restrict Jenkins access to authorized users only

Contributing
Feel free to submit issues and pull requests.
License
[Add your chosen license here]
