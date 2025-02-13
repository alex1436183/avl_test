pipeline {
    agent { label 'minion' }

    stages {
        stage('Checkout') {
            steps {
                cleanWs()  // Удаляем старые файлы
                git branch: 'main', url: 'https://github.com/alex1436183/avl_test'
                sh 'ls -la'  // Проверяем, что все файлы на месте
            }
        }

        stage('Run Timer Script') {
            steps {
                script {
                    // Запуск timer.py
                    sh 'python3 timer.py'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Используем python3 -m pip для установки pytest
                    sh 'python3 -m pip install pytest'
                    // Запуск тестов
                    sh 'pytest --maxfail=1 --disable-warnings -q'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
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
            junit '**/target/test-*.xml'
            publishHTML([
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
