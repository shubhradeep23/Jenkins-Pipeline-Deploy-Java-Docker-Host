# Jenkins Pipeline: Deploy Java App on Docker Host running on EC2 Instance using Ansible

Pre-requisites in Jenkins: Download the Word Document from https://github.com/shubhradeep23/jenkins-sonar-eks to first setup Jenkins, SonarQube, Maven, Docker, but skip the EKS creation part.

A. Install Ansible Plugin in Jenkins

B. Manage Jenkins> Manage Credentials> Add Credentials> 
   - Kind: SSH Username with private key
   - ID: docker-host
   - Username: ubuntu
   - Private Key: select 'Enter Directly'> Copy Paste the entire content of pem file you used to create AWS EC2 instances downloaded on your personal machine
   - Click OK
   
C. Manage Jenkins> Global Tool Configuration> Add Ansible:
   - Name: ansible
   - Path to ansible executables directory: /usr/local/bin/
   - Save


Step 1: Create a ubuntu EC2 t2.micro with same pem keys> default VPC> default Security Group> change storage to 30GB (optional).

Step 2: Create empty github repo without README.md

Step 3: Download the zip file: https://github.com/shubhradeep23/Jenkins-Pipeline-Deploy-Java-Docker-Host-Ansible.git

Step 4: Create empty folder & extract above zip to this empty folder.

Step 5: Open above folder you created in VS Code editor & make the following changes:

change 1:
Under ansible directory, open hosts> add private-ip under [docker-host]

change 2:
Under ansible directory, open docker.yml> change image name from the command mentioned below-
ansible.builtin.shell: sudo docker run -d -p 8085:8085 "image-name:{{image_tag}}"

change 3:
Under ansible directory, open install-docker.yml
default_container_image: "image-name:{{image_tag}}"

change 4:
In Jenkinsfile, under 'environment' block, change the following variables: 
imagename = your-dockerhub-username/dockerhub-repo-or-image-name  
tag = image-tag
docker_host = private-ip-ec2-docker-host
registryCredential = 'dockerhub-cred'
dockerImage = ''
CHECK_URL = "http://public-ip-ec2-docker-host:port no/greeting"          

Step 6: VS Code Editor, open new terminal> type the below commands to commit & push the changes you made to the empty github repo you created in step2.
git init
git commit -m "Jenkins Pipeline: Deploy Java App on Remote Docker EC2 using Ansible "
git branch -M main
git remote add origin https://your-github-repo.git
git push -u origin main

Verify if java app is reachable through web browser with url: public-ip-ec2-docker-host:8085/greeting
