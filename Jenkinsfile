pipeline {
    // Убираем агент верхнего уровня, чтобы каждый этап мог выбрать свой

    agent { 
        label 'worker' 
    }

    stages {
        // --- Этап 1: Используем агент Gradle ---
        stage('Test Backend Agent') {
            steps {
                ws('/home/jenkins/agent/workspace/tryyyyy')
                container(name: 'java') {  
                    checkout scm
                    sh '''
                        echo "--- Загрузка ENV ---"
                        set -a && . /etc/secrets/secret.env && set +a
                        
                        update-ca-certificates

                        
                    '''
                }
            }
        }

        // --- Этап 2: Используем агент Node ---
        stage('Test Frontend Agent') {
            steps {
                ws('/home/jenkins/agent/workspace/tryyyyy')
                container(name: 'front') { 
                    echo '--- Проверка Инструмента внутри контейнера node ---'
                    sh 'node --version' 
                    sh 'npm --version' 
                    sh 'echo "Working directory: $(pwd)"'
                }
            }
        }
    }

}
