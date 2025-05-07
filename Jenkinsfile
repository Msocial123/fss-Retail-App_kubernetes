pipeline {
    agent any

    environment {

        EMAIL_PASS=''
        EMAIL_USER=''
        PORT=''
        MONGODB_URI=''
        SESSION_SECRET=''
        
    }

    tools {

        nodejs "NodeJS"

    }


    stages {

        stage('Git checkout') {
            steps {
                git url: 'https://github.com/hanzel-sc/fss-Retail-App_kubernetes.git', branch: 'test'
            }
        }
        
        stage('Fetch Secrets from Vault') {
            steps {
                withVault(
                    configuration: [vaultUrl: 'http://127.0.0.1:8200', vaultCredentialId: 'vault-token'],
                    vaultSecrets: [[
                        path: 'secret/fss-retail-app',
                        secretValues: [
                            [envVar: 'EMAIL_PASS', vaultKey: 'EMAIL_PASS'],
                            [envVar: 'EMAIL_USER', vaultKey: 'EMAIL_USER'],
                            [envVar: 'PORT', vaultKey: 'PORT'],
                            [envVar: 'MONGODB_URI', vaultKey: 'MONGODB_URI'],
                            [envVar: 'SESSION_SECRET', vaultKey: 'SESSION_SECRET']
                        ]
                    ]]
                ) {
                    echo "Secrets retrieved successfully."
                    echo "Email password is: ${env.EMAIL_PASS.take(2)}***" 
                }
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    sh 'npm install'
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    sh 'pm2 delete all || true'
                    sh "pm2 start server.js --name nodejs-backend"
                    echo "Server is up and running"
                }
            }
        }
    }

    post {
        failure {
            echo 'Build failed! Check Jenkins logs.'
        }
        success {
            echo 'Successfully deployed NodeJS application with secrets from Vault.'
        }
    }
}
