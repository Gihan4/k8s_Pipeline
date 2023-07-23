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

        stage('Remove old Images from local machine') {
            steps {
                // Delete from Jenkins local server
                echo "Stopping and removing containers and images on Jenkins server..."
                sh "docker rmi -f gihan4/k8sapp:latest"
            }
        }

        stage('Clone') {
            steps {
                echo "Cloning repository..."
                sh 'git clone https://github.com/Gihan4/redis_flask.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                echo "Building Docker flask image ..."
                dir('redis_flask') {
                    sh 'docker-compose build --no-cache -t gihan4/k8sredis:${BUILD_NUMBER} -t gihan4/k8sredis:latest -f .'
                }
            }
        }

        stage('Push app Image to Docker Hub') {
            steps {
                echo "Pushing Docker image to Docker Hub..."
                sh 'docker push --all-tags gihan4/k8sredis'
            }
        }

        

        stage('Connect Cluster') {
            steps {
                echo "connecting to the cluster..."
                sh 'gcloud container clusters get-credentials autopilot-cluster-1 --region us-central1 --project formidable-hold-392607'
            }
        }


        stage('Deploy to Kubernetes') {
            steps {
                // Deploy the Docker image to the Kubernetes cluster using kubectl with the manifest yaml file
                echo "deploy docker flask on cluster..."
                sh 'kubectl apply -f /var/lib/jenkins/k8s-manifests/new-manifest.yaml'
            }
        }

        stage('Test Cluster') {
            steps {
                
                script {
        
                    def SERVICE_NAME = "flask-service"
        
                    // Get the external IP of the Load Balancer associated with the Service.
                    def EXTERNAL_IP = sh(script: "kubectl get svc $SERVICE_NAME -o jsonpath='{.status.loadBalancer.ingress[0].ip}'", returnStdout: true).trim()
        
                    if (EXTERNAL_IP.isEmpty()) {
                        echo "Load Balancer IP not found. Check if the Load Balancer is provisioned and the Service is exposed correctly."
                        currentBuild.result = 'FAILURE'
                        return
                    }
        
                    def FLASK_APP_URL = "http://$EXTERNAL_IP"
        
                    // Send a GET request to your Flask application and store the HTTP status code in a variable.
                    def HTTP_STATUS = sh(script: "curl -s -o /dev/null -w %{http_code} $FLASK_APP_URL", returnStdout: true).trim().toInteger()
        
                    // Check if the HTTP status code is 200 (OK) or any other expected status code.
                    def EXPECTED_STATUS_CODE = 200
        
                    if (HTTP_STATUS == EXPECTED_STATUS_CODE) {
                        echo "Flask application is running successfully. HTTP status code: $HTTP_STATUS"
                    } else {
                        echo "Flask application is not running as expected. HTTP status code: $HTTP_STATUS"
                        currentBuild.result = 'FAILURE'
                        return
                    }
                }
                
           }
            
    }
}
