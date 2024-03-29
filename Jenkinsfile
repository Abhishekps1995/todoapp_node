pipeline {
    agent any
    
    stages{
        stage('Code'){
            steps{
                git url: 'https://github.com/Abhishekps1995/todoapp_node.git', branch: 'main' 
            }
        }

        stage('Build Docker Image') {
            steps {
                // Build the Docker image from the Dockerfile in your repository
                sh 'docker build -t abhishekps/node-todo-test:latest .'
            }
        }

           stage('Push'){
            steps{
                withCredentials([usernamePassword(credentialsId: 'DockeHub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
        	     sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
                 sh 'docker push abhishekps/node-todo-test:latest'
                }
            }
        }

        stage('Deploy with Docker Compose') {
            steps {
                // Install Docker on the remote server
                // sh 'sudo ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@13.233.195.166 "sudo apt update && sudo apt install -y apt-transport-https ca-certificates curl software-properties-common"'
                // sh 'sudo ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@13.233.195.166 "sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg"'
                // sh 'sudo ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@13.233.195.166 "sudo echo \"deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable\" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null"'
                // sh 'sudo ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@13.233.195.166 "sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io"'

                // Copy the Docker Compose YAML to the remote server using SCP
                sh "sudo scp -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem /home/ubuntu/todoapp_node/docker-compose.yaml ubuntu@13.233.195.166:/home/ubuntu/docker-compose.yaml"

                // SSH into the remote server and deploy the application with Docker Compose
                sh "sudo ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@13.233.195.166 'sudo systemctl start docker'"
                sh "sudo ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@13.233.195.166 'sudo systemctl enable docker'"
                sh "sudo ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@13.233.195.166 'docker --version'"
                sh "sudo ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@13.233.195.166 'sudo docker-compose -f /home/ubuntu/docker-compose.yaml down && docker-compose -f /home/ubuntu/docker-compose.yaml up -d'"
            }
        }
    }
}
