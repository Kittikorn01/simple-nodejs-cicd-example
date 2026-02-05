pipeline {
  agent {
    kubernetes {
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
    stage('Prepare') {
      steps {
        container('my-builder') {
          sh 'npm ci'
        }
      }
    }

    stage('Test') {
      steps {
        container('my-builder') {
          sh 'npm run test'
        }
      }
    }

    stage('Deploy') {
      steps {
        container('my-builder') {
          sh 'npm install -g vercel@latest'
          // ตัด vercel link ออกแล้วใช้การระบุชื่อโปรเจกต์ตรงๆ ในการ deploy
          sh "vercel --token ${VERCEL_TOKEN} --name ${VERCEL_PROJECT_NAME} --prod --confirm --yes"
        }
      }
    }
  }

  // แนะนำให้เอา post ออกไปก่อนจนกว่าจะตั้งค่าให้ Mocha สร้างไฟล์ XML ได้จริง
  /*
  post {
    always {
       junit 'test-results/junit.xml'
    }
  }
  */
}