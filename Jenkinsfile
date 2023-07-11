pipeline {
    agent any

    //triggers {
        // The pipeline is triggered every minute to check for changes in the git
        // pollSCM('*/1 * * * *')
    //}

    environment {
        testip = sh(
            script: "aws ec2 describe-instances --region eu-central-1 --filters 'Name=tag:Environment,Values=Compose1' --query 'Reservations[].Instances[].PublicIpAddress' --output text",
            returnStdout: true
        ).trim()
    }

    stages {
        stage('Cleanup') {
            steps {
                echo "Cleaning up..."
                // removes all files and directories in the current working directory 
                sh 'rm -rf *'
            }
        }

        stage('Stop and Remove Containers and Images from all machines') {
            steps {
                // Delete from Jenkins server
                echo "Stopping and removing containers and images on Jenkins server..."
                sh "docker stop \$(docker ps -aq) || true"
                sh "docker rm \$(docker ps -aq) || true"
                // except for the latest version of the image
                sh """
                    docker images --format '{{.Repository}}:{{.Tag}}' gihan4/appimage:* |
                    awk -F: '{print \$2}' |
                    sort -r |
                    tail -n +2 |
                    xargs -I {} docker rmi gihan4/appimage:{} || true
                """
        
                // Delete from AWS Test instance
                echo "Stopping and removing containers and images on AWS Test instance..."
                sh """
                    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/Gihan4.pem ec2-user@${testip} '
                        docker stop \$(docker ps -aq) || true &&
                        docker rm \$(docker ps -aq) || true &&
                        docker images --format "{{.Repository}}:{{.Tag}}" gihan4/appimage:* |
                        awk -F: "{print \\\$2}" |
                        sort -r |
                        tail -n +2 |
                        xargs -I {} docker rmi gihan4/appimage:{} || true'
                """
                
                // Delete from AWS Production instance
                echo "Stopping and removing containers and images on AWS Production instance..."
                sh """
                    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/Gihan4.pem ec2-user@${prodip} '
                        docker stop \$(docker ps -aq) || true &&
                        docker rm \$(docker ps -aq) || true &&
                        docker images --format "{{.Repository}}:{{.Tag}}" gihan4/appimage:* |
                        awk -F: "{print \\\$2}" |
                        sort -r |
                        tail -n +2 |
                        xargs -I {} docker rmi gihan4/appimage:{} || true'
                """
            }
        }



        stage('Install Docker on test instance') {
            steps {
              
                echo "Installing Docker and Docker Compose on AWS test instance..."
                sh '''
                    ssh -o StrictHostKeyChecking=no -i /var/lib/jenkins/.ssh/Gihan4.pem ec2-user@${testip} '
                        sudo yum update -y &&
                        sudo yum install -y docker &&
                        sudo service docker start &&
                        sudo usermod -aG docker ec2-user &&
                        sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose &&
                        sudo chmod +x /usr/local/bin/docker-compose'
                '''
            }
        }

        stage('Install EKS dependencies') {
            steps {
                script {
                    sshCommand remote: remote, command: '''
                        # Install kubectl
                        curl -o kubectl https://amazon-eks.s3.us-west-2.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
                        chmod +x ./kubectl
                        sudo mv kubectl /usr/local/bin/kubectl
                        
                        # Install eksctl
                        sudo curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | sudo tar xz -C /usr/local/bin
                    '''
                }
            }
        }

        stage('Clone') {
            steps {
                echo "Cloning repository..."
                sh 'git clone https://github.com/Gihan4/docker_compose_project.git'
                sh 'ls'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker image from app..."
                dir('docker_compose_project') {
                    sh 'docker build -t gihan4/appimage:${BUILD_NUMBER} -t gihan4/appimage:latest -f app/Dockerfile .'
                }
            }
        }

        stage('Push app Image to Docker Hub') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                sh 'docker push --all-tags gihan4/appimage'
            }
        }

        stage('Deploy on Test server') {
            steps {
                echo "Deploying and testing on AWS test instance..."
                    // pulls the Docker app image onto the EC2 instance.
                    // sh "ssh -o StrictHostKeyChecking=no -i $HOME/.ssh/Gihan4.pem ec2-user@${testip} 'docker pull gihan4/appimage:latest'"
                    // Copy the docker-compose.yml file to the EC2 instance.
                    sh "scp -o StrictHostKeyChecking=no -i $HOME/.ssh/Gihan4.pem /var/lib/jenkins/workspace/PIpeline_compose/docker_compose_project/docker-compose.yml ec2-user@${testip}:~/docker-compose.yml"
                    // Copy the database folder to the EC2 instance.
                    sh "scp -o StrictHostKeyChecking=no -i $HOME/.ssh/Gihan4.pem -r /var/lib/jenkins/workspace/PIpeline_compose/docker_compose_project/database ec2-user@${testip}:~/"
                    // SSH into the EC2 instance and run docker-compose that will pull the app image from Docker Hub.
                    sh "ssh -o StrictHostKeyChecking=no -i $HOME/.ssh/Gihan4.pem ec2-user@${testip} 'docker-compose -f ~/docker-compose.yml up -d'"
            }
        }

        stage('Check Flask API') {
            steps {
                echo "Checking Flask API..."
    
                // Make an HTTP request to the Flask API endpoint
                script {
                    def response = sh script: "curl -s -o /dev/null -w '%{http_code}' http://${testip}:5000", returnStdout: true
                    def statusCode = response.trim()
                    
                    if (statusCode == '200') {
                        echo "Flask API is running successfully. Response code: ${statusCode}"
                    } else {
                        error "Flask API is not running. Response code: ${statusCode}"
                    }
                }
            }
        }


        stage('Deploy on Production server') {
            steps {
                echo "Deploying after testing on AWS production instance..."
                    // Copy the docker-compose.yml file to the EC2 instance.
                    sh "scp -o StrictHostKeyChecking=no -i $HOME/.ssh/Gihan4.pem /var/lib/jenkins/workspace/PIpeline_compose/docker_compose_project/docker-compose.yml ec2-user@${prodip}:~/docker-compose.yml"
                    // Copy the database folder to the EC2 instance.
                    sh "scp -o StrictHostKeyChecking=no -i $HOME/.ssh/Gihan4.pem -r /var/lib/jenkins/workspace/PIpeline_compose/docker_compose_project/database ec2-user@${prodip}:~/"
                    // SSH into the EC2 instance and run docker-compose that will pull the app image from Docker Hub and run the services.
                    sh "ssh -o StrictHostKeyChecking=no -i $HOME/.ssh/Gihan4.pem ec2-user@${prodip} 'docker-compose -f ~/docker-compose.yml up -d'"
            }
        }


    }
}
