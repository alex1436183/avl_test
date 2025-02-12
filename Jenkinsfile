pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                cleanWs() // Удаляем старые файлы
                git branch: 'main', url: 'https://github.com/alex1436183/avl_test'
            }
        }

        stage('Run Script') {
            steps {
                script {
                    sh 'python3 timer.py'
                }
            }
        }
    }

    post {
        always {
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
