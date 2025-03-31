// pipeline {
//     agent any

//     parameters {
//         choice(name: 'DEPLOY_ENV', choices: ['blue', 'green'], description: 'Select Deployment Environment (Blue or Green)')
//         booleanParam(name: 'SWITCH_TRAFFIC', defaultValue: false, description: 'Switch traffic to the selected environment')
//     }

//     environment {
//         DOCKERHUB_REPO = "dhruv2412"
//         KUBE_NAMESPACE = "webapps"
//     }

//     stages {

//         stage('Checkout Code') {
//             steps {
//                 git branch: 'main', credentialsId: 'blue-green', url: 'https://github.com/Dhruvlunagaria/Diamondlabour_linux'
//             }
//         }

//         stage('Deploy Blue or Green') {
//             steps {
//                 script {
//                     def deploymentFile = "k8s/${params.DEPLOY_ENV}-deployment.yaml"
//                     def serviceFile = "k8s/${params.DEPLOY_ENV}-service.yaml"
                    
//                     sh """
//                         kubectl apply -f ${deploymentFile} -n ${KUBE_NAMESPACE}
//                         kubectl apply -f ${serviceFile} -n ${KUBE_NAMESPACE}
//                     """
//                 }
//             }
//         }

//         stage('Deploy Ingress') {
//             steps {
//                 script {
//                     sh "kubectl apply -f k8s/ingress.yaml -n ${KUBE_NAMESPACE}"
//                 }
//             }
//         }

//         stage('Switch Traffic') {
//             when {
//                 expression { return params.SWITCH_TRAFFIC }
//             }
//             steps {
//                 script {
//                     def newEnv = params.DEPLOY_ENV
                    
//                     sh '''
//                         kubectl patch ingress app-ingress -p '{
//                             "spec": { 
//                                 "rules": [{
//                                     "host": "app.minikube",
//                                     "http": {
//                                         "paths": [{
//                                             "path": "/",
//                                             "pathType": "Prefix",
//                                             "backend": {
//                                                 "service": {
//                                                     "name": "''' + newEnv + '''-service",
//                                                     "port": { "number": 80 }
//                                                 }
//                                             }
//                                         }]
//                                     }
//                                 }]
//                             }
//                         }' -n ${KUBE_NAMESPACE}
//                     '''
//                     echo "Traffic switched to ${newEnv} environment!"
//                 }
//             }
//         }

//         stage('Verify Deployment') {
//             steps {
//                 script {
//                     def verifyEnv = params.DEPLOY_ENV
                    
//                     sh """
//                         kubectl get pods -l version=${verifyEnv} -n ${KUBE_NAMESPACE}
//                         kubectl get svc ${verifyEnv}-service -n ${KUBE_NAMESPACE}
//                     """
//                 }
//             }
//         }
//     }
// }










// pipeline {
//     agent any

//     parameters {
//         choice(name: 'DEPLOY_ENV', choices: ['blue', 'green'], description: 'Select Deployment Environment (Blue or Green)')
//         booleanParam(name: 'SWITCH_TRAFFIC', defaultValue: false, description: 'Switch traffic to the selected environment')
//     }

//     environment {
//         DOCKERHUB_REPO = "dhruv2412"
//         KUBE_NAMESPACE = "webapps"
//     }

//     stages {

//         stage('Checkout Code') {
//             steps {
//                 git branch: 'main', credentialsId: 'blue-green', url: 'https://github.com/Dhruvlunagaria/Diamondlabour_linux'
//             }
//         }

//         stage('Deploy Blue or Green') {
//             steps {
//                 script {
//                     def deploymentFile = "k8s/${params.DEPLOY_ENV}-deployment.yaml"
//                     def serviceFile = "k8s/${params.DEPLOY_ENV}-service.yaml"

//                     sh """
//                         echo "🚀 Deploying ${params.DEPLOY_ENV} environment..."
//                         kubectl apply -f ${deploymentFile} -n ${KUBE_NAMESPACE}
//                         kubectl apply -f ${serviceFile} -n ${KUBE_NAMESPACE}
//                     """
//                 }
//             }
//         }

//         stage('Deploy Ingress') {
//             steps {
//                 script {
//                     sh """
//                         echo "🌐 Deploying Ingress..."
//                         kubectl apply -f k8s/ingress.yaml -n ${KUBE_NAMESPACE}
//                     """
//                 }
//             }
//         }

//         stage('Switch Traffic') {
//             when {
//                 expression { return params.SWITCH_TRAFFIC }
//             }
//             steps {
//                 script {
//                     def newEnv = params.DEPLOY_ENV

//                     sh """
//                         echo "🔄 Switching Traffic to ${newEnv} environment..."
                        
//                         # 🚀 Delete & Reapply Ingress
//                         kubectl delete ingress app-ingress -n ${KUBE_NAMESPACE} || true
//                         kubectl apply -f k8s/ingress.yaml -n ${KUBE_NAMESPACE}
                        
//                         # 🔄 Update Traffic Routing
//                         kubectl patch ingress app-ingress -n ${KUBE_NAMESPACE} --type='merge' -p '{
//                             "spec": { 
//                                 "rules": [{
//                                     "host": "app.minikube",
//                                     "http": {
//                                         "paths": [{
//                                             "path": "/",
//                                             "pathType": "Prefix",
//                                             "backend": {
//                                                 "service": {
//                                                     "name": "${newEnv}-service",
//                                                     "port": { "number": 81 }
//                                                 }
//                                             }
//                                         }]
//                                     }
//                                 }]
//                             }
//                         }'

//                         echo "✅ Traffic successfully switched to ${newEnv} environment!"
//                     """
//                 }
//             }
//         }

//         stage('Verify Deployment') {
//             steps {
//                 script {
//                     def verifyEnv = params.DEPLOY_ENV
                    
//                     sh """
//                         echo "🔍 Verifying ${verifyEnv} environment..."
//                         kubectl get pods -l version=${verifyEnv} -n ${KUBE_NAMESPACE}
//                         kubectl get svc ${verifyEnv}-service -n ${KUBE_NAMESPACE}
//                         kubectl get ingress -n ${KUBE_NAMESPACE}
//                     """
//                 }
//             }
//         }
//     }
// }




pipeline {
    agent any

    parameters {
        choice(name: 'DEPLOY_ENV', choices: ['blue', 'green'], description: 'Select Deployment Environment (Blue or Green)')
        booleanParam(name: 'SWITCH_TRAFFIC', defaultValue: false, description: 'Switch traffic to the selected environment')
    }

    environment {
        DOCKERHUB_REPO = "dhruv2412"
        KUBE_NAMESPACE = "webapps"
        INGRESS_NAME = "app-ingress"
    }

    stages {

        stage('Checkout Code') {
            steps {
                script {
                    echo "📥 Checking out code..."
                    git branch: 'main', credentialsId: 'blue-green', url: 'https://github.com/Dhruvlunagaria/Diamondlabour_linux'
                }
            }
        }

        stage('Deploy Blue or Green') {
            steps {
                script {
                    def deploymentFile = "k8s/${params.DEPLOY_ENV}-deployment.yaml"
                    def serviceFile = "k8s/${params.DEPLOY_ENV}-service.yaml"

                    echo "🚀 Deploying ${params.DEPLOY_ENV} environment..."
                    
                    if (fileExists(deploymentFile)) {
                        sh "kubectl apply -f ${deploymentFile} -n ${KUBE_NAMESPACE}"
                    } else {
                        error "❌ Deployment file ${deploymentFile} not found!"
                    }

                    if (fileExists(serviceFile)) {
                        sh "kubectl apply -f ${serviceFile} -n ${KUBE_NAMESPACE}"
                    } else {
                        error "❌ Service file ${serviceFile} not found!"
                    }
                }
            }
        }

        stage('Deploy Ingress') {
            steps {
                script {
                    echo "🌐 Deploying Ingress..."
                    if (fileExists("k8s/ingress.yaml")) {
                        sh "kubectl apply -f k8s/ingress.yaml -n ${KUBE_NAMESPACE}"
                    } else {
                        error "❌ Ingress file k8s/ingress.yaml not found!"
                    }
                }
            }
        }

        stage('Switch Traffic') {
            when {
                expression { return params.SWITCH_TRAFFIC }
            }
            steps {
                script {
                    echo "🔄 Checking current traffic route..."

                    // Get the currently active service from the ingress
                    def ACTIVE_SERVICE = sh(script: """
                        kubectl get ingress ${INGRESS_NAME} -n ${KUBE_NAMESPACE} -o=jsonpath='{.spec.rules[0].http.paths[0].backend.service.name}' 2>/dev/null || echo "unknown"
                    """, returnStdout: true).trim()

                    echo "✅ Currently active: ${ACTIVE_SERVICE}"

                    // Debugging: print the current ingress configuration
                    echo "🔍 Current ingress configuration:"
                    sh """
                        kubectl get ingress ${INGRESS_NAME} -n ${KUBE_NAMESPACE} -o yaml
                    """

                    // Check if the active service is correctly identified
                    if (ACTIVE_SERVICE == "unknown") {
                        error "❌ Failed to retrieve active service from the ingress!"
                    }

                    // Determine the new service based on the current one
                    def NEW_SERVICE = (ACTIVE_SERVICE == "green-service") ? "blue-service" : "green-service"
                    def NEW_PORT = (NEW_SERVICE == "green-service") ? "81" : "80"  // Set port based on the service

                    echo "🔄 Switching traffic to ${NEW_SERVICE} (port ${NEW_PORT})..."

                    // Delete the existing ingress and apply the updated ingress with the new service
                    sh """
                        kubectl delete ingress ${INGRESS_NAME} -n ${KUBE_NAMESPACE} --ignore-not-found=true
                        kubectl apply -f k8s/ingress.yaml -n ${KUBE_NAMESPACE}

                        // Patch ingress to route traffic to the new service
                        kubectl patch ingress ${INGRESS_NAME} -n ${KUBE_NAMESPACE} --type='merge' -p '{
                            "spec": { 
                                "rules": [{
                                    "host": "app.minikube",
                                    "http": {
                                        "paths": [{
                                            "path": "/",
                                            "pathType": "Prefix",
                                            "backend": {
                                                "service": {
                                                    "name": "'${NEW_SERVICE}'",
                                                    "port": { "number": ${NEW_PORT} }
                                                }
                                            }
                                        }]
                                    }
                                }]
                            }
                        }' || echo "❌ Failed to switch traffic to ${NEW_SERVICE}"
                    """

                    echo "✅ Traffic successfully switched to ${NEW_SERVICE}!"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    echo "🔍 Verifying deployment..."
                    sh """
                        kubectl get pods -n ${KUBE_NAMESPACE}
                        kubectl get svc -n ${KUBE_NAMESPACE}
                        kubectl get ingress ${INGRESS_NAME} -n ${KUBE_NAMESPACE} -o yaml
                    """
                }
            }
        }
    }

    post {
        success {
            echo "✅ Blue-Green Deployment Successful!"
        }
        failure {
            echo "❌ Deployment failed. Check logs and debug."
        }
    }
}
















