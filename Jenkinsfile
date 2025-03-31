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

    environment {
        K8S_NAMESPACE = "webapps"
        INGRESS_NAME = "app-ingress"
    }

    stages {
        stage('Checkout Code') {
            steps {
                script {
                    echo "📥 Checking out code from Git..."
                    git branch: 'main', credentialsId: 'blue-green', url: 'https://github.com/Dhruvlunagaria/Diamondlabour_linux'
                }
            }
        }

        stage('Deploy Blue or Green') {
            steps {
                script {
                    echo "🔄 Checking current traffic route..."
                    
                    def activeServiceOutput = sh(script: "kubectl get ingress ${INGRESS_NAME} -n ${K8S_NAMESPACE} -o=jsonpath='{.spec.rules[0].http.paths[0].backend.service.name}'", returnStdout: true).trim()

                    if (activeServiceOutput) {
                        ACTIVE_SERVICE = activeServiceOutput
                    } else {
                        echo "⚠️ Failed to fetch ACTIVE_SERVICE, defaulting to blue-service"
                        ACTIVE_SERVICE = "blue-service" // Fallback
                    }
                    
                    echo "✅ Current active service: ${ACTIVE_SERVICE}"
                    
                    // Switch to the new service
                    NEW_SERVICE = (ACTIVE_SERVICE == "blue-service") ? "green-service" : "blue-service"
                    NEW_PORT = (NEW_SERVICE == "blue-service") ? "80" : "81"
                    
                    echo "🚀 Deploying ${NEW_SERVICE}..."
                    
                    sh "kubectl apply -f k8s/${NEW_SERVICE}-deployment.yaml -n ${K8S_NAMESPACE}"
                    sh "kubectl apply -f k8s/${NEW_SERVICE}-service.yaml -n ${K8S_NAMESPACE}"
                }
            }
        }

        stage('Deploy Ingress') {
            steps {
                script {
                    echo "🌐 Deploying Ingress..."
                    sh "kubectl apply -f k8s/ingress.yaml -n ${K8S_NAMESPACE}"
                }
            }
        }

        stage('Switch Traffic') {
            steps {
                script {
                    echo "🔀 Switching traffic to ${NEW_SERVICE}..."
                    
                    sh """kubectl patch ingress ${INGRESS_NAME} -n ${K8S_NAMESPACE} --type=merge -p '{
                        "spec": { 
                            "rules": [{
                                "host": "app.minikube",
                                "http": {
                                    "paths": [{
                                        "path": "/",
                                        "pathType": "Prefix",
                                        "backend": {
                                            "service": {
                                                "name": "${NEW_SERVICE}",
                                                "port": { "number": ${NEW_PORT} }
                                            }
                                        }
                                    }]
                                }
                            }]
                        }
                    }'"""
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    echo "🧐 Verifying new deployment..."
                    sh "kubectl get pods -n ${K8S_NAMESPACE}"
                    sh "kubectl get svc -n ${K8S_NAMESPACE}"
                    sh "kubectl get ingress ${INGRESS_NAME} -n ${K8S_NAMESPACE} -o yaml"
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


















