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

                    withKubeConfig([credentialsId: 'k8-token', contextName: 'minikube', namespace: KUBE_NAMESPACE]) {
                        sh """
                            kubectl apply -f ${deploymentFile} -n ${KUBE_NAMESPACE}
                            kubectl apply -f ${serviceFile} -n ${KUBE_NAMESPACE}
                        """
                    }
                }
            }
        }

        stage('Deploy Ingress') {
            steps {
                script {
                    withKubeConfig([credentialsId: 'k8-token', contextName: 'minikube', namespace: KUBE_NAMESPACE]) {
                        sh "kubectl apply -f k8s/ingress.yaml -n ${KUBE_NAMESPACE}"
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
                    def newEnv = params.DEPLOY_ENV

                    withKubeConfig([credentialsId: 'k8-token', contextName: 'minikube', namespace: KUBE_NAMESPACE]) {
                        sh '''
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
                                                        "name": "' + newEnv + '-service",
                                                        "port": { "number": 80 }
                                                    }
                                                }
                                            }]
                                        }
                                    }]
                                }
                            }' -n ${KUBE_NAMESPACE}
                        '''
                    }
                    echo "Traffic switched to ${newEnv} environment!"
                }
            }
        }

        stage('Verify Deployment') {
            steps {
                script {
                    def verifyEnv = params.DEPLOY_ENV

                    withKubeConfig([credentialsId: 'k8-token', contextName: 'minikube', namespace: KUBE_NAMESPACE]) {
                        sh """
                            kubectl get pods -l version=${verifyEnv} -n ${KUBE_NAMESPACE}
                            kubectl get svc ${verifyEnv}-service -n ${KUBE_NAMESPACE}
                        """
                    }
                }
            }
        }
    }
}
