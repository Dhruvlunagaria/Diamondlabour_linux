pipeline {
    agent any
    environment {
        KUBE_CONTEXT = "minikube"
        NAMESPACE = "default"  
        DOCKERHUB_REPO = "your-dockerhub-repo"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git 'https://your-repo-url.git'
            }
        }
        stage('Deploy New Version') {
            steps {
                script {
                    def currentVersion = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name'", returnStdout: true).trim()
                    
                    if (currentVersion == "blue-app") {
                        echo "🔵 Blue is Active. Deploying Green..."
                        sh """
                            kubectl apply -f k8s/green-deployment.yaml
                            kubectl set image deployment/green-app frontend=${DOCKERHUB_REPO}/frontend-green:latest --record
                            kubectl set image deployment/green-app backend=${DOCKERHUB_REPO}/backend-green:latest --record
                        """
                    } else {
                        echo "🟢 Green is Active. Deploying Blue..."
                        sh """
                            kubectl apply -f k8s/blue-deployment.yaml
                            kubectl set image deployment/blue-app frontend=${DOCKERHUB_REPO}/frontend-blue:latest --record
                            kubectl set image deployment/blue-app backend=${DOCKERHUB_REPO}/backend-blue:latest --record
                        """
                    }
                }
            }
        }
        stage('Switch Traffic') {
            steps {
                script {
                    def currentVersion = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name'", returnStdout: true).trim()
                    
                    if (currentVersion == "blue-app") {
                        echo "✅ Switching traffic to Green..."
                        sh "kubectl label deployment green-app app=active --overwrite"
                    } else {
                        echo "✅ Switching traffic to Blue..."
                        sh "kubectl label deployment blue-app app=active --overwrite"
                    }
                }
            }
        }
        stage('Health Check') {
            steps {
                script {
                    def status = sh(script: 'curl -f http://your-app.com', returnStatus: true)
                    if (status != 0) {
                        error("❌ Deployment failed! Rolling back...")
                    }
                }
            }
        }
        stage('Delete Old Version') {
            steps {
                script {
                    def currentVersion = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name'", returnStdout: true).trim()
                    
                    if (currentVersion == "blue-app") {
                        echo "🔵 Blue is Active. Deleting Green..."
                        sh 'kubectl delete -f k8s/green-deployment.yaml'
                    } else {
                        echo "🟢 Green is Active. Deleting Blue..."
                        sh 'kubectl delete -f k8s/blue-deployment.yaml'
                    }
                }
            }
        }
    }
    post {
        failure {
            echo "❌ Deployment failed! Rolling back to previous version..."
            script {
                def previousVersion = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name'", returnStdout: true).trim()
                
                if (previousVersion == "blue-app") {
                    sh """
                        kubectl apply -f k8s/blue-deployment.yaml
                        kubectl apply -f k8s/ingress.yaml
                    """
                } else {
                    sh """
                        kubectl apply -f k8s/green-deployment.yaml
                        kubectl apply -f k8s/ingress.yaml
                    """
                }
            }
        }
        success {
            echo "✅ Deployment successful!"
        }
    }
}
