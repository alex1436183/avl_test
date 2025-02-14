pipeline {
    agent { label 'minion' }
    stages {
        stage('Checkout') {
            steps {
                cleanWs()
                git branch: 'main', url: 'https://github.com/alex1436183/avl_test'
            }
        }
        stage('Run Calculator Tests') {
            steps {
                sh 'python3 -m unittest discover -v'
            }
        }
        stage('Run Calculator Interactive') {
            steps {
                sh '''python3 calculator.py <<EOF
1
5
7
EOF'''
            }
        }
        stage('Create Directory for Deployment') {
            steps {
                sh 'ssh -i $SSH_KEY jenkins@minion "mkdir -p ${HOME}/deploy"'
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh '''tar czf - * | ssh -i $SSH_KEY jenkins@minion "tar xzf - -C ${HOME}/deploy"'''
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
                body: "<p>Jenkins job <b>${env.JOB_NAME}</b> (<b>${env.BUILD_NUMBER}</b>) успешно выполнен!</p><p>Проверить можно тут: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>",
                to: 'alex1436183@gmail.com',
                mimeType: 'text/html'
            )
        }
        failure {
            echo 'Build failed!'
            emailext(
                subject: "Jenkins Job FAILED: ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "<p>Jenkins job <b>${env.JOB_NAME}</b> (<b>${env.BUILD_NUMBER}</b>) завершился с ошибкой!</p><p>Логи можно посмотреть тут: <a href='${env.BUILD_URL}'>${env.BUILD_URL}</a></p>",
                to: 'alex1436183@gmail.com',
                mimeType: 'text/html'
            )
        }
    }
}
