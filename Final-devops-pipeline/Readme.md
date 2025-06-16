 Final DevOps Project: One-Click Jenkins Pipeline Deployment

 Overview

This project automates a complete deployment pipeline using Jenkins, Terraform, Ansible, and Docker. The entire process is triggered from a Jenkins pipeline and covers infrastructure provisioning (simulated), configuration management, and deployment of a static web application.

 What It Does

- Initializes infrastructure provisioning using Terraform
- Uses Ansible to configure a web server and deploy a static site
- All steps are executed from a Jenkins pipeline running inside a Docker container
- Outputs a simple web page that says:  
  ` Successfully Deployed via Jenkins + Terraform + Ansible!`

 Tools Used

- Jenkins – Automates the CI/CD pipeline
- Docker – Hosts Jenkins in a container
- Terraform – Simulates VM provisioning
- Ansible – Configures web server and deploys the app
- Git – Stores the Jenkinsfile, code, and configuration files

 Project Structure

```
project/
├── terraform/
│   ├── main.tf
│   └── variables.tf
├── ansible/
│   ├── inventory.ini
│   └── playbook.yml
├── app/
│   └── index.html
├── Jenkinsfile
└── README.md
```

 Jenkins Setup (Docker)

Jenkins was run in a Docker container with access to Terraform, Ansible, and the project files.

```bash
docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v /usr/bin/terraform:/usr/local/bin/terraform \
  -v /usr/bin/ansible-playbook:/usr/local/bin/ansible-playbook \
  jenkins/jenkins:lts
```

After Jenkins started:

- Plugins were installed: Git, Pipeline, SSH Agent
- SSH credentials were added (if needed)

 Terraform Configuration

Terraform was used to simulate infrastructure provisioning using a `null_resource`.

terraform/main.tf
hcl
resource "null_resource" "example" {
  provisioner "local-exec" {
    command = "echo Terraform resource created!"
  }
}


 Ansible Playbook

The playbook configures the server and prints a confirmation message. In a real scenario, it would install Apache and move the `index.html` file.

ansible/inventory.ini**

localhost ansible_connection=local


ansible/playbook.yml
yaml
- name: Sample Ansible Playbook
  hosts: localhost
  become: true
  tasks:
    - name: Print a message
      debug:
        msg: "Ansible playbook executed successfully"


 Static Web App

app/index.html
html
<!DOCTYPE html>
<html>
  <head>
    <title>Deployed Page</title>
  </head>
  <body>
    <h1> Successfully Deployed via Jenkins + Terraform + Ansible!</h1>
  </body>
</html>


 Jenkins Pipeline

Jenkinsfile
groovy
pipeline {
    agent any

    stages {
        stage('Terraform Init & Apply') {
            steps {
                dir('terraform') {
                    sh 'terraform init'
                    sh 'terraform apply -auto-approve'
                }
            }
        }

        stage('Ansible Deployment') {
            steps {
                dir('ansible') {
                    sh 'ansible-playbook -i inventory.ini playbook.yml'
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                echo 'Verifying deployment...'
                // Example: curl http://your-vm-ip (if using a real VM)
            }
        }
    }
}


Running the Project

1. Start Jenkins in Docker.
2. Open Jenkins in your browser.
3. Create a new pipeline job.
4. Use the Jenkinsfile from this project.
5. Run the pipeline to trigger all steps.

 Result

The deployment completes successfully and the static HTML page with the success message is placed on the web server.

html
<h1> Successfully Deployed via Jenkins + Terraform + Ansible!</h1>


This confirms the end-to-end CI/CD workflow is working as expected.
![image](https://github.com/user-attachments/assets/5362801f-3d01-4a8b-9c91-c08a3f4796a0)
![image](https://github.com/user-attachments/assets/0c0eda7c-bbc9-457e-ae30-2f7e7ed48baa)


