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
//                 git branch: 'main', credentialsId: 'blue-green', url: 'https://github.com/Dhruvlunagaria/Diamondlabour_linux'
//             }
//         }

//         stage('Deploy New Version') {
//             steps {
//                 script {
//                     def currentVersion = sh(script: """kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name' || echo none""", returnStdout: true).trim()

//                     if (currentVersion == "none") {
//                         echo "🚀 First Deployment! Deploying Green..."
//                         sh """
//                             kubectl apply -f k8s/green-deployment.yaml
//                             kubectl set image deployment/green-app frontend=${DOCKERHUB_REPO}/green-frontend:latest --record
//                             kubectl set image deployment/green-app backend=${DOCKERHUB_REPO}/green-backend:latest --record
//                         """
//                     } else if (currentVersion == "blue-app") {
//                         echo "🔵 Blue is Active. Deploying Green..."
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
//                     // def currentVersion = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=':metadata.name'", returnStdout: true).trim()
//                     def currentVersion = sh(script: "kubectl get pods -l app=active --no-headers -o custom-columns=':metadata.labels.app'", returnStdout: true).trim()
                  
//                     if (currentVersion == "none" || currentVersion == "blue") {
//                         echo "✅ Switching traffic to Green..."
//                         sh """
//                             kubectl label pods -l app=green app=active --overwrite
//                             kkubectl label pods -l app=blue app- --overwrite
//                         """
//                     } else {
//                         echo "✅ Switching traffic to Blue..."
//                         sh """
//                             kubectl label pods -l app=blue app=active --overwrite
//                             kubectl label pods -l app=green app- --overwrite
//                         """
//                     }

//                     echo "⏳ Waiting for new pods to become ready..."
//                     sleep(time: 30, unit: "SECONDS")
//                 }
//             }
//         }

//         stage('Health Check') {
//             steps {
//                 script {
//                     def maxRetries = 5
//                     def waitTime = 10  // seconds
//                     def success = false

//                     for (int i = 0; i < maxRetries; i++) {
//                         echo "🔍 Health Check Attempt ${i+1}/${maxRetries}..."
//                         def status = sh(script: 'curl -f http://diamondlab.com', returnStatus: true)

//                         if (status == 0) {
//                             echo "✅ Service is healthy!"
//                             success = true
//                             break
//                         }

//                         echo "⚠️ Service not ready yet. Retrying in ${waitTime} seconds..."
//                         sleep(time: waitTime, unit: "SECONDS")
//                     }

//                     if (!success) {
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
//                         sh 'kubectl delete -f k8s/green-deployment.yaml || true'
//                     } else {
//                         echo "🟢 Green is Active. Deleting Blue..."
//                         sh 'kubectl delete -f k8s/blue-deployment.yaml || true'
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
        KUBECONFIG = "/var/lib/jenkins/.kube/config"// Adjust if needed
        BLUE_IMAGE = "dhruv2412/blue-frontend:latest"
        GREEN_IMAGE = "dhruv2412/green-frontend:latest"
        BLUE_DEPLOY = "blue-app"
        GREEN_DEPLOY = "green-app"
        BLUE_SERVICE = "blue-service"
        GREEN_SERVICE = "green-service"
        INGRESS_NAME = "app-ingress"
        DOMAIN_NAME = "diamondlab.com"
    }

    stages {
        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: 'blue-green', url: 'https://github.com/Dhruvlunagaria/Diamondlabour_linux.git'
            }
        }

        stage('Deploy New Version') {
            steps {
                script {
                    // Get active deployment
                    def activeApp = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=:metadata.name", returnStdout: true).trim()
                    
                    // Set new deployment & image
                    def newApp = (activeApp == BLUE_DEPLOY) ? GREEN_DEPLOY : BLUE_DEPLOY
                    def newImage = (newApp == BLUE_DEPLOY) ? BLUE_IMAGE : GREEN_IMAGE

                    echo "Deploying new version: ${newApp} with image: ${newImage}"
                    
                    // Apply the new deployment
                    sh "kubectl apply -f k8s/${newApp}-deployment.yaml"
                    sh "kubectl label deployment ${newApp} app=inactive --overwrite"
                    sh "kubectl set image deployment/${newApp} frontend=${newImage}"
                }
            }
        }

        stage('Switch Traffic') {
            steps {
                script {
                    // Get active deployment
                    def activeApp = sh(script: "kubectl get deployments -l app=active --no-headers -o custom-columns=:metadata.name", returnStdout: true).trim()

                    // Determine new active deployment
                    def newActive = (activeApp == BLUE_DEPLOY) ? GREEN_DEPLOY : BLUE_DEPLOY
                    def newService = (newActive == BLUE_DEPLOY) ? BLUE_SERVICE : GREEN_SERVICE

                    echo "Switching traffic to ${newActive} via ${newService}"

                    // Switch labels
                    sh "kubectl label deployment ${activeApp} app=inactive --overwrite || true"
                    sh "kubectl label deployment ${newActive} app=active --overwrite"

                    // Update Ingress
                    sh "kubectl patch ingress ${INGRESS_NAME} -p '{\"spec\":{\"rules\":[{\"host\":\"${DOMAIN_NAME}\",\"http\":{\"paths\":[{\"path\":\"/\",\"pathType\":\"Prefix\",\"backend\":{\"service\":{\"name\":\"${newService}\",\"port\":{\"number\":80}}}}]}}]}}'"
                }
            }
        }

        stage('Delete Old Version') {
            steps {
                script {
                    def inactiveApp = sh(script: "kubectl get deployments -l app=inactive --no-headers -o custom-columns=:metadata.name", returnStdout: true).trim()
                    
                    echo "Deleting old version: ${inactiveApp}"
                    sh "kubectl delete deployment ${inactiveApp} || true"
                }
            }
        }
    }

    post {
        success {
            echo "✅ Blue-Green Deployment Successful!"
        }
        failure {
            echo "❌ Deployment failed! Rolling back to previous version..."
            sh "kubectl apply -f k8s/${GREEN_DEPLOY}-deployment.yaml || true"
            sh "kubectl patch ingress ${INGRESS_NAME} -p '{\"spec\":{\"rules\":[{\"host\":\"${DOMAIN_NAME}\",\"http\":{\"paths\":[{\"path\":\"/\",\"pathType\":\"Prefix\",\"backend\":{\"service\":{\"name\":\"${GREEN_SERVICE}\",\"port\":{\"number\":80}}}}]}}]}}' || true"
        }
    }
}


