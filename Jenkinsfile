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

        stage('Set up Virtual Environment') {
            steps {
                script {
                    // Создаем виртуальное окружение
                    sh 'python3 -m venv venv'
                    // Активируем виртуальное окружение
                    sh 'source venv/bin/activate'
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Устанавливаем pytest в виртуальное окружение
                    sh '''
                        source venv/bin/activate
                        python3 -m pip install --upgrade pip
                        pip install pytest
                    '''
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    // Запуск тестов в виртуальном окружении
                    sh '''
                        source venv/bin/activate
                        pytest --maxfail=1 --disable-warnings -q
                    '''
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
