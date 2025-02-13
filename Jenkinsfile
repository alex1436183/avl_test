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
        stage('Deploy') {
            steps {
                withCredentials([sshUserPrivateKey(credentialsId: 'agent-ssh-key', keyFileVariable: 'SSH_KEY')]) {
                    script {
                        // Добавляем ключ хоста в known_hosts
                        sh "ssh-keyscan -H minion >> ~/.ssh/known_hosts"

                        // Создаем директорию на удаленной машине
                        sh "ssh -i \$SSH_KEY jenkins@minion 'mkdir -p /path/to/deploy'"

                        // Копируем файлы на удаленную машину
                        sh "scp -i \$SSH_KEY ${WORKSPACE}/calculator.py jenkins@minion:/path/to/deploy/"
                        sh "scp -i \$SSH_KEY ${WORKSPACE}/test_calculator.py jenkins@minion:/path/to/deploy/"
                    }
                }
            }
        }
    }
    post
