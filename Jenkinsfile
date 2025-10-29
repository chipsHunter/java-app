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
                        echo "--- Загрузка ENV ---"
                        set -a && . /etc/secrets/secret.env && set +a
                        ls -lah /usr/local/share/ca-certificates
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
                    sh "ls -lah"
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
                        echo "--- Загрузка ENV ---"
                        set -a && . /etc/secrets/secret.env && set +a
                        ls -lah /usr/local/share/ca-certificates
                        kubectl config current-context
                        kubectl auth can-i create deployment --namespace default
                        helm version
                    """
                }
            }
        }
       
    }
}
