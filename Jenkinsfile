pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                }
            }
            steps {
                sh '''
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                ls -la
                test -f build/index.html
                npm test
                '''
            }
        }
        stage('Test Stage'){
            steps{
                sh '''
                echo "Test Stage"
                '''
            }
        }
     }
}