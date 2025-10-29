pipeline {
    agent none

    environment {
        REGISTRY = 'registry.stasian.net'
        BUILD_TAG = "${env.BUILD_ID}"
        CACHE_REPO = "${REGISTRY}/kaniko-cache"
    }

    stages {
        stage('Checkout & Setup') {
            agent { 
                label 'docker-worker' 
            } 
            steps {
                container(name: 'jnlp') { 
                    checkout scm 
                    
                    sh '''
                        ls -lah .
                    '''
                }
            }
        }
        
        stage('Build Backend') {
            agent { 
                label 'docker-worker' 
            } 
            steps {
                container(name: 'kaniko') {
                    sh """
                    /kaniko/executor \\
                        --context=./back-end \\
                        --dockerfile=./back-end/Dockerfile \\
                        --destination=${REGISTRY}/backend:${BUILD_TAG} \\
                        --cache=true \\
                        --cache-repo=${CACHE_REPO}
                    """
                }
            }
        }
        
        stage('Build Frontend') {
            agent { 
                label 'docker-worker' 
            } 
            steps {
                container(name: 'kaniko') {
                    sh """
                    /kaniko/executor \\
                        --context=./front-end \\
                        --dockerfile=./front-end/Dockerfile \\
                        --destination=${REGISTRY}/frontend:${BUILD_TAG} \\
                        --cache=true \\
                        --cache-repo=${CACHE_REPO}
                    """
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
