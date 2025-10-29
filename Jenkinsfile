pipeline {
    agent none

    environment {
        REGISTRY = 'registry.stasian.net'
        BUILD_TAG = "${env.BUILD_ID}"
    }

    stages {
        stage('Checkout & Setup') {
            agent { 
                label 'docker-worker' 
            } 
            steps {
                container(name: 'docker') { 
                    checkout scm 
                    
                    sh '''
                        set -a && . /etc/secrets/secret.env && set +a
                    '''
                }
            }
        }
        
        stage('Build Backend') {
            agent { 
                label 'docker-worker' 
            } 
            steps {
                container(name: 'docker') { 
                    sh "docker build -t ${REGISTRY}/backend:${BUILD_TAG} ./back-end/"
                    sh "docker push ${REGISTRY}/backend:${BUILD_TAG}"
                }
            }
        }
        
        stage('Build Frontend') {
            agent { 
                label 'docker-worker' 
            } 
            steps {
                container(name: 'docker') {
                    echo "--- Сборка Frontend ---"
                    // 1. Сборка Frontend (Docker делает всю работу, включая npm install)
                    sh "docker build -t ${REGISTRY}/frontend:${BUILD_TAG} ./front-end/"
                    sh "docker push ${REGISTRY}/frontend:${BUILD_TAG}"
                }
            }
        }
        stage('Happy Helming!') {
            agent { 
                label 'kuber' 
            } 
            steps {
                container(name: 'k8s') {
                    sh """

                        kubectl auth can-i create deployment --namespace default
                        helm version
                        helm upgrade --install app .infrastructure/charts/app/ \\
                            --namespace default \\
                            --wait \\
                            --set backend.image.repository=${REGISTRY}/backend\\
                            --set backend.image.tag=${BUILD_TAG} \\
                            --set frontend.image=${REGISTRY}/frontend \\
                            --set frontend.tag=${BUILD_TAG} \\
                            --set frontend.domainName="app.stasian.net"
                        kubectl get pods -n default
                    """
                }
            }
        }
       
    }
}
