pipeline {
    agent any

    stages {
        stage('Cleanup') {
            steps {
                echo "Cleaning up..."
                // removes all files and directories in the current working directory 
                sh 'rm -rf *'
            }
        }

        stage('Stop and Remove Containers and Images from local machine') {
            steps {
                // Delete from Jenkins local server
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
            }
        }


        stage('Clone') {
            steps {
                echo "Cloning repository..."
                sh 'git clone https://github.com/Gihan4/Flask.git'
                sh 'ls'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker flask image ..."
                dir('Flask') {
                    sh 'docker build -t gihan4/flask8s:${BUILD_NUMBER} -t gihan4/appimage:latest -f .'
                }
            }
        }

        stage('Push app Image to Docker Hub') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                sh 'docker push --all-tags gihan4/appimage'
            }
        }

        stage('Check Cluster') {
            steps {
                echo "Checking if the cluster is up and running..."
                sh 'kubectl get nodes'
            }

        stage('Deploy to Kubernetes') {
            steps {
                // Deploy the Docker image to the Kubernetes cluster using kubectl with the manifest yaml file
                echo "deploy docker flask on cluster..."
                sh 'kubectl apply -f /var/lib/jenkins/k8s-manifests/your-manifest.yaml'
            }
        }
        

    }
}
