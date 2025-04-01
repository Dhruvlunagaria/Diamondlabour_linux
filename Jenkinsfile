
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
        INGRESS_BLUE = "k8s/ingress-blue.yaml"
        INGRESS_GREEN = "k8s/ingress-green.yaml"
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

        stage('Switch Traffic') {
            when {
                expression { return params.SWITCH_TRAFFIC }
            }
            steps {
                script {
                    echo "🔄 Checking current traffic route..."

                    def ACTIVE_SERVICE = sh(script: """
                        kubectl get ingress -n ${KUBE_NAMESPACE} -o=jsonpath='{.items[0].spec.rules[0].http.paths[0].backend.service.name}' || echo "unknown"
                    """, returnStdout: true).trim()

                    echo "✅ Currently active: ${ACTIVE_SERVICE}"

                    if (ACTIVE_SERVICE == "unknown") {
                        error "❌ Failed to retrieve active service from the ingress!"
                    }

                    // Determine which ingress file to apply
                    def NEW_INGRESS = (ACTIVE_SERVICE == "green-service") ? INGRESS_BLUE : INGRESS_GREEN

                    echo "🔄 Switching traffic using ${NEW_INGRESS}..."

                    sh """
                        kubectl delete ingress -n ${KUBE_NAMESPACE} --all --ignore-not-found=true
                        kubectl apply -f ${NEW_INGRESS} -n ${KUBE_NAMESPACE}
                    """

                    echo "✅ Traffic successfully switched!"
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
                        kubectl get ingress -n ${KUBE_NAMESPACE} -o yaml
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















