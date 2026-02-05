pipeline {
    agent {
        kubernetes {
            // ต้องระบุ label เสมอใน Declarative Pipeline
            label 'nodejs-agent'
            yaml '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: my-builder
    image: node:20-alpine
    command:
    - cat
    tty: true
'''
        }
    }

    environment {
        VERCEL_PROJECT_NAME = 'devops04-simple-nodejs-cicd'
        VERCEL_TOKEN = credentials('devops04-vercel-token') 
    }

    stages {
        stage('Install Dependencies') {
            steps {
                container('my-builder') {
                    sh 'npm ci'
                }
            }
        }

        stage('Run Test') {
            steps {
                container('my-builder') {
                    sh 'npm run test'
                }
            }
        }

        stage('Deploy to Vercel') {
            steps {
                container('my-builder') {
                    // ติดตั้ง vercel
                    sh 'npm install -g vercel@latest'
                    // ใช้ --yes เพื่อไม่ต้องรอการยืนยัน และระบุชื่อโปรเจกต์ตรงๆ
                    sh "vercel --token ${VERCEL_TOKEN} --name ${VERCEL_PROJECT_NAME} --prod --confirm --yes"
                }
            }
        }
    }
}