pipeline {
    agent { label 'minion' }  // Указываем агент с меткой 'minion'

    environment {
        DEPLOY_KEY = 'deploy-ssh-key' // Указываем ID для секретного ключа Jenkins
    }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()  // Удаляем старые файлы
                git branch: 'main', url: 'https://github.com/alex1436183/avl_test'
            }
        }

        stage('Compile') {
            steps {
                script {
                    sh 'python3 setup.py install'  // Пример компиляции
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'pytest --maxfail=1 --disable-warnings -q'  // Запуск тестов
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Используем SSH-ключи из Jenkins credentials
                    withCredentials([sshUserPrivateKey(credentialsId: 'deploy-ssh-key', keyFileVariable: 'SSH_KEY')]) {
                        sh """
                            scp -i \$SSH_KEY my_project user@minion:/path/to/deploy
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            junit '**/target/test-*.xml'  // Публикуем JUnit отчеты
            publishHTML([  // Публикуем HTML отчеты
                reportDir: 'build/reports',
                reportFiles: 'index.html',
                reportName: 'HTML Report'
            ])
            echo 'Build finished'
        }

        success {
            echo 'Build was successful!'
            emailext(
                subject: "Jenkins Job SUCCESS: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>Jenkins job <b>${env.JOB_NAME}</b> (<b>${env.BUILD_NUMBER}</b>) успешно выполнен! </p>
                    <p>Проверить можно тут: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                """,
                to: 'alex1436183@gmail.com',
                mimeType: 'text/html'
            )
        }

        failure {
            echo 'Build failed!'
            emailext(
                subject: "Jenkins Job FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    <p>Jenkins job <b>${env.JOB_NAME}</b> (<b>${env.BUILD_NUMBER}</b>) завершился с ошибкой! </p>
                    <p>Логи можно посмотреть тут: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                """,
                to: 'alex1436183@gmail.com',
                mimeType: 'text/html'
            )
        }
    }
}
