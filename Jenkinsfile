pipeline {
    agent { label 'minion' }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()  // Очистка рабочего пространства
                git branch: 'main', url: 'https://github.com/alex1436183/avl_test'
            }
        }

        stage('Run Calculator Tests') {
            steps {
                sh 'python3 -m unittest discover -v'  // Запуск тестов для калькулятора
            }
        }

        stage('Run Calculator Interactive') {
            steps {
                sh '''python3 calculator.py <<EOF
1
5
7
EOF'''  // Пример интерактивного теста для калькулятора
            }
        }

        stage('Create Directory for Deployment') {
            steps {
                sh 'mkdir -p /path/to/deploy'  // Создание каталога для деплоя
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'agent-ssh-key', keyFileVariable: 'SSH_KEY')]) {
                    sh '''tar czf - my_project | ssh -i $SSH_KEY jenkins@minion "mkdir -p /path/to/deploy && tar xzf - -C /path/to/deploy"'''  // Деплой с использованием SSH
                }
            }
        }
    }

    post {
        always {
            echo 'Build finished'  // Сообщение, которое будет выведено всегда
        }

        success {
            echo 'Build was successful!'  // Сообщение об успешной сборке
            emailext(
                subject: "Jenkins Job SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "<p>Jenkins job <b>${env.JOB_NAME}</b> (<b>${env.BUILD_NUMBER}</b>) успешно выполнен!</p><p>Проверить можно тут: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>",
                to: 'alex1436183@gmail.com',
                mimeType: 'text/html'
            )
        }

        failure {
            echo 'Build failed!'  // Сообщение о неудачной сборке
            emailext(
                subject: "Jenkins Job FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "<p>Jenkins job <b>${env.JOB_NAME}</b> (<b>${env.BUILD_NUMBER}</b>) завершился с ошибкой!</p><p>Логи можно посмотреть тут: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>",
                to: 'alex1436183@gmail.com',
                mimeType: 'text/html'
            )
        }
    }
}
