// pipeline {
//     agent any
//     environment {
//         KUBE_CONTEXT = "minikube"
//         NAMESPACE = "default"  
//         DOCKERHUB_REPO = "dhruv2412"
//     }
//     stages {
//         stage('Checkout Code') {
//             steps {
//                   git branch: 'main', credentialsId: 'blue-green', url: 'https://github.com/Dhruvlunagaria/Diamondlabour_linux'

//             }
//         }
//         stage('Deploy New Version') {
//             steps {
//                 script {
                    
//                    def currentVersion = sh(script: """kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name' || echo none""", returnStdout: true).trim()

//                     if (currentVersion == "none" || currentVersion == "blue-app") {
//                         echo "🔵 Blue is Active or No Active Deployment Found. Deploying Green..."
//                         sh """
//                             kubectl apply -f k8s/green-deployment.yaml
//                             kubectl set image deployment/green-app frontend=${DOCKERHUB_REPO}/green-frontend:latest --record
//                             kubectl set image deployment/green-app backend=${DOCKERHUB_REPO}/green-backend:latest --record
//                         """
//                     } else {
//                         echo "🟢 Green is Active. Deploying Blue..."
//                         sh """
//                             kubectl apply -f k8s/blue-deployment.yaml
//                             kubectl set image deployment/blue-app frontend=${DOCKERHUB_REPO}/blue-frontend:latest --record
//                             kubectl set image deployment/blue-app backend=${DOCKERHUB_REPO}/blue-backend:latest --record
//                         """
//                     }
//                 }
//             }
//         }
//         stage('Switch Traffic') {
//             steps {
//                 script {
//                     def currentVersion = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name'", returnStdout: true).trim()
                    
//                     if (currentVersion == "blue-app") {
//                         echo "✅ Switching traffic to Green..."
//                         sh "kubectl label deployment green-app app=active --overwrite"
//                     } else {
//                         echo "✅ Switching traffic to Blue..."
//                         sh "kubectl label deployment blue-app app=active --overwrite"
//                     }
//                 }
//             }
//         }
//         stage('Health Check') {
//             steps {
//                 script {
//                     def status = sh(script: 'curl -f http://diamondlab.com', returnStatus: true)
//                     if (status != 0) {
//                         error("❌ Deployment failed! Rolling back...")
//                     }
//                 }
//             }
//         }
//         stage('Delete Old Version') {
//             steps {
//                 script {
//                     def currentVersion = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name'", returnStdout: true).trim()
                    
//                     if (currentVersion == "blue-app") {
//                         echo "🔵 Blue is Active. Deleting Green..."
//                         sh 'kubectl delete -f k8s/green-deployment.yaml'
//                     } else {
//                         echo "🟢 Green is Active. Deleting Blue..."
//                         sh 'kubectl delete -f k8s/blue-deployment.yaml'
//                     }
//                 }
//             }
//         }
//     }
//     post {
//         failure {
//             echo "❌ Deployment failed! Rolling back to previous version..."
//             script {
//                 def previousVersion = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name'", returnStdout: true).trim()
                
//                 if (previousVersion == "blue-app") {
//                     sh """
//                         kubectl apply -f k8s/blue-deployment.yaml
//                         kubectl apply -f k8s/ingress.yaml
//                     """
//                 } else {
//                     sh """
//                         kubectl apply -f k8s/green-deployment.yaml
//                         kubectl apply -f k8s/ingress.yaml
//                     """
//                 }
//             }
//         }
//         success {
//             echo "✅ Deployment successful!"
//         }
//     }
// }

pipeline {
    agent any
    environment {
        KUBE_CONTEXT = "minikube"
        NAMESPACE = "default"  
        DOCKERHUB_REPO = "dhruv2412"
    }
    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: 'blue-green', url: 'https://github.com/Dhruvlunagaria/Diamondlabour_linux'
            }
        }

        stage('Deploy New Version') {
            steps {
                script {
                    def currentVersion = sh(script: """kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name' || echo none""", returnStdout: true).trim()

                    if (currentVersion == "none") {
                        echo "🚀 First Deployment! Deploying Green..."
                        sh """
                            kubectl apply -f k8s/green-deployment.yaml
                            kubectl set image deployment/green-app frontend=${DOCKERHUB_REPO}/green-frontend:latest --record
                            kubectl set image deployment/green-app backend=${DOCKERHUB_REPO}/green-backend:latest --record
                        """
                    } else if (currentVersion == "blue-app") {
                        echo "🔵 Blue is Active. Deploying Green..."
                        sh """
                            kubectl apply -f k8s/green-deployment.yaml
                            kubectl set image deployment/green-app frontend=${DOCKERHUB_REPO}/green-frontend:latest --record
                            kubectl set image deployment/green-app backend=${DOCKERHUB_REPO}/green-backend:latest --record
                        """
                    } else {
                        echo "🟢 Green is Active. Deploying Blue..."
                        sh """
                            kubectl apply -f k8s/blue-deployment.yaml
                            kubectl set image deployment/blue-app frontend=${DOCKERHUB_REPO}/blue-frontend:latest --record
                            kubectl set image deployment/blue-app backend=${DOCKERHUB_REPO}/blue-backend:latest --record
                        """
                    }
                }
            }
        }

        stage('Switch Traffic') {
            steps {
                script {
                    def currentVersion = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name'", returnStdout: true).trim()

                    if (currentVersion == "none" || currentVersion == "blue-app") {
                        echo "✅ Switching traffic to Green..."
                        sh """
                            kubectl label deployment green-app app=active --overwrite
                            kubectl label deployment blue-app app- || true
                        """
                    } else {
                        echo "✅ Switching traffic to Blue..."
                        sh """
                            kubectl label deployment blue-app app=active --overwrite
                            kubectl label deployment green-app app- || true
                        """
                    }

                    echo "⏳ Waiting for new pods to become ready..."
                    sleep(time: 30, unit: "SECONDS")
                }
            }
        }

        stage('Health Check') {
            steps {
                script {
                    def maxRetries = 5
                    def waitTime = 10  // seconds
                    def success = false

                    for (int i = 0; i < maxRetries; i++) {
                        echo "🔍 Health Check Attempt ${i+1}/${maxRetries}..."
                        def status = sh(script: 'curl -f http://diamondlab.com', returnStatus: true)

                        if (status == 0) {
                            echo "✅ Service is healthy!"
                            success = true
                            break
                        }

                        echo "⚠️ Service not ready yet. Retrying in ${waitTime} seconds..."
                        sleep(time: waitTime, unit: "SECONDS")
                    }

                    if (!success) {
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
                        sh 'kubectl delete -f k8s/green-deployment.yaml || true'
                    } else {
                        echo "🟢 Green is Active. Deleting Blue..."
                        sh 'kubectl delete -f k8s/blue-deployment.yaml || true'
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
