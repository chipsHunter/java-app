pipeline {
    agent { 
        label 'docker-builder' 
    } 

    environment {
        REGISTRY = 'registry.stasian.net'
        BUILD_TAG = "${env.BUILD_ID}"
    }

    stages {
        stage('Checkout & Setup') {
            steps {
                container(name: 'docker') { /
                    checkout scm 
                    
                    sh '''
                        echo "--- Загрузка ENV ---"
                        set -a && . /etc/secrets/secret.env && set +a
                        update-ca-certificates
                    '''
                }
            }
        }
        
        stage('Build Backend') {
            steps {
                container(name: 'docker') { 
                    sh "ls -lah"
                    sh "docker build -t ${REGISTRY}/backend:${BUILD_TAG} ./back-end/"
                    sh "docker push ${REGISTRY}/backend:${BUILD_TAG}"
                }
            }
        }

        // --- СТАДИЯ 3: СБОРКА И ПУШ ФРОНТЕНДА ---
        stage('Build Frontend') {
            steps {
                container(name: 'docker') {
                    echo "--- Сборка Frontend ---"
                    // 1. Сборка Frontend (Docker делает всю работу, включая npm install)
                    sh "docker build -t ${REGISTRY}/frontend:${BUILD_TAG} ./front-end/"
                    sh "docker push ${REGISTRY}/frontend:${BUILD_TAG}"
                }
            }
        }

       
    }
    
    post { always { cleanWs() } }
}
