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

        stage('Login to Docker Hub') {
            steps {
                // Securely handle Docker Hub credentials
                withDockerRegistry(credentialsId: 'docker') {
                withCredentials([usernamePassword(credentialsId: 'docker', passwordVariable: 'DOCKER_HUB_PASS', usernameVariable: 'DOCKER_HUB_USER')]) {
                    sh 'docker login -u $DOCKER_HUB_USER --password-stdin'
                }
            }
        }

        stage('Push') {
            steps {
                // Push the Docker image to Docker Hub
                sh 'docker push abhishekps/node-todo-test:latest'
            }
        }
        stage('Deploy with Docker Compose') {
            steps {
                // Install Docker on the remote server
                sh 'ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@35.154.118.175 "sudo apt update && sudo apt install -y apt-transport-https ca-certificates curl software-properties-common"'
                sh 'ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@35.154.118.175 "curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg"'
                sh 'ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@35.154.118.175 "echo \"deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable\" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null"'
                sh 'ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@35.154.118.175 "sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io"'

                // Copy the Docker Compose YAML to the remote server using SCP
                sh "scp -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem /home/ubuntu/todoapp_node/docker-compose.yml ubuntu@35.154.118.175:/home/ubuntu/docker-compose.yml"

                // SSH into the remote server and deploy the application with Docker Compose
                sh "ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@35.154.118.175 'sudo systemctl start docker'"
                sh "ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@35.154.118.175 'sudo systemctl enable docker'"
                sh "ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@35.154.118.175 'docker --version'"
                sh "ssh -o StrictHostKeyChecking=no -i /home/ubuntu/ec2_micro_keypair.pem ubuntu@35.154.118.175 'docker-compose -f /home/ubuntu/docker-compose.yml down && docker-compose -f /home/ubuntu/docker-compose.yml up -d'"
            }
        }
    }
}
