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
                    sh 'python3 tiner.py'
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
        }
        failure {
            echo 'Build failed!'
        }
    }
}
