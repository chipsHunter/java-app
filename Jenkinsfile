pipeline {
    // Убираем агент верхнего уровня, чтобы каждый этап мог выбрать свой

    agent { 
        label 'worker' 
    }

    stages {
        // --- Этап 1: Используем агент Gradle ---
        stage('Test Backend Agent') {
            steps {
                container(name: 'java') {  
                    sh '''
                        echo "--- Загрузка ENV ---"
                        set -a && . /etc/secrets/secret.env && set +a
                        
                        sudo update-ca-certificates
                    '''
                }
            }
        }

        // --- Этап 2: Используем агент Node ---
        stage('Test Frontend Agent') {
            steps {
                 // Указываем контейнер внутри Pod'а агента
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
