pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                cleanWs() 
                git branch: 'main', url: 'https://github.com/alex1436183/avl_test'
            }
        }

        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'agent-ssh-key', keyFileVariable: 'SSH_KEY')]) {
                    sh '''
                        tar czf - my_project | ssh -i $SSH_KEY jenkins@minion "mkdir -p /path/to/deploy && tar xzf - -C /path/to/deploy"
                    '''
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
                    <p>Jenkins job <b>${env.JOB_NAME}</b> (<b>${env.BUILD_NUMBER}</b>) successfully completed! </p>
                    <p>Check it here: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
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
                    <p>Jenkins job <b>${env.JOB_NAME}</b> (<b>${env.BUILD_NUMBER}</b>) failed! </p>
                    <p>Logs are available here: <a href="${env.BUILD_URL}">${env.BUILD_URL}</a></p>
                """,
                to: 'alex1436183@gmail.com',
                mimeType: 'text/html'
            )
        }
    }
}
