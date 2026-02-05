pipeline {
    agent {
        kubernetes {
            label 'nodejs-agent'
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: my-builder
    image: node:20-alpine
    command: ['cat']
    tty: true
'''
        }
    }

    environment {
        VERCEL_PROJECT_NAME = 'devops04-simple-nodejs-cicd'
    }

    stages {
        stage('Install & Test') {
            steps {
                container('my-builder') {
                    sh 'npm ci'
                    sh 'npm run test'
                }
            }
        }

        stage('Deploy') {
            steps {
                container('my-builder') {
                    // ใช้ ID ให้ตรงกับที่ตั้งไว้ใน Jenkins
                    withCredentials([string(credentialsId: 'devops04-vercel-token', variable: 'VERCEL_TOKEN')]) {
                        sh 'npm install -g vercel@latest'
                        sh "vercel --token ${VERCEL_TOKEN} --name ${VERCEL_PROJECT_NAME} --prod --confirm --yes"
                    }
                }
            }
        }
    }
}