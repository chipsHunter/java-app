pipeline {
    agent { 
        label 'worker' 
    } 

    stages {
        stage('Test Backend Agent') {
            steps {
                ws('/home/jenkins/agent/workspace/tryyyyy') {
                    container(name: 'java') {  
                        checkout scm 
                        
                        sh '''
                            echo "--- Загрузка ENV ---"
                            set -a && . /etc/secrets/secret.env && set +a
                            gradle --version
                            update-ca-certificates
                            ls -lah .
                        '''
                        script {
                            def appImage = docker.build("registry.stasian.net/backend:${env.BUILD_NUMBER}", 
                                                       "-f back-end/Dockerfile back-end")
                            appImage.push()
                        }
                    } 
                } 
            }
        }
        stage('Test Frontend Agent') {
            steps {
                ws('/home/jenkins/agent/workspace/tryyyyy') {
                    container(name: 'front') {
                        echo '--- Проверка Инструмента внутри контейнера node ---'
                        sh 'node --version' 
                        sh 'npm --version' 
                    } 
                } 
            }
        }
    }

}
