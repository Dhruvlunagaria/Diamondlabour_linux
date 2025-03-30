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

pipeline {
    agent any

    parameters {
        choice(name: 'DEPLOY_ENV', choices: ['blue', 'green'], description: 'Select Deployment Environment (Blue or Green)')
        booleanParam(name: 'SWITCH_TRAFFIC', defaultValue: false, description: 'Switch traffic to the selected environment')
    }

    environment {
        DOCKERHUB_REPO = "dhruv2412"
        KUBE_NAMESPACE = "webapps"
    }

    stages {

        stage('Checkout Code') {
            steps {
                git branch: 'main', credentialsId: 'blue-green', url: 'https://github.com/Dhruvlunagaria/Diamondlabour_linux'
            }
        }

        stage('Deploy Blue or Green') {
            steps {
                script {
                    def deploymentFile = "k8s/${params.DEPLOY_ENV}-deployment.yaml"
                    def serviceFile = "k8s/${params.DEPLOY_ENV}-service.yaml"
                    
                    sh """
                        echo "🚀 Deploying ${params.DEPLOY_ENV} environment..."
                        kubectl apply -f ${deploymentFile} -n ${KUBE_NAMESPACE}
                        kubectl apply -f ${serviceFile} -n ${KUBE_NAMESPACE}
                    """
                }
            }
        }

        stage('Deploy Ingress') {
            steps {
                script {
                    sh """
                        echo "🌐 Deploying Ingress..."
                        kubectl apply -f k8s/ingress.yaml -n ${KUBE_NAMESPACE}
                    """
                }
            }
        }

        stage('Switch Traffic') {
            when {
                expression { return params.SWITCH_TRAFFIC }
            }
            steps {
                script {
                    def newEnv = params.DEPLOY_ENV

                    sh """
                        echo "🔄 Switching Traffic to ${newEnv} environment..."
                        
                        # 🚀 Delete & Reapply Ingress
                        kubectl delete ingress app-ingress -n ${KUBE_NAMESPACE} || true
                        kubectl apply -f k8s/ingress.yaml -n ${KUBE_NAMESPACE}
                        
                        # 🔄 Update Traffic Routing
                        kubectl patch ingress app-ingress -p '{
                            "spec": { 
                                "rules": [{
                                    "host": "app.minikube",
                                    "http": {
                                        "paths": [{
                                            "path": "/",
                                            "pathType": "Prefix",
                                            "backend": {
                                                "service": {
                                                    "name": "''' + newEnv + '''-service",
                                                    "port": { "number": 81 }
                                                }
                                            }
                                        }]
                                    }
                                }]
                            }
                        }' -n ${KUBE_NAMESPACE}

                        echo "✅ Traffic successfully switched to ${newEnv} environment!"
                    """
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    def verifyEnv = params.DEPLOY_ENV
                    
                    sh """
                        echo "🔍 Verifying deployment..."
                        kubectl get pods -l version=${verifyEnv} -n ${KUBE_NAMESPACE}
                        kubectl get svc ${verifyEnv}-service -n ${KUBE_NAMESPACE}
                    """
                }
            }
        }
    }
}

















