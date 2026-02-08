pipeline {
    agent any
//Setting up environment varibales
    environment{
        NETLIFY_SITE_ID = 'ccfcc967-b439-4a2a-b7a2-3ff22e577fdc'
        NETLIFY_AUTH_TOKEN = credentials('netlify-jenkins')
        REACT_APP_VERSION = "1.0.${BUILD_ID}"
    }
// Starting the pipeline with stages

//Building the application
    stages {
        stage('Docker Build'){
            steps{
                sh 'docker build -t my-playwrite .'
            }
        }
        /*stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                sh '''
                    ls -la
                    node --version
                    npm --version
                    npm ci
                    npm run build
                '''
            }
        }
//Testing the Build: Parallel testing of Unit and E2E testing
        stage('Tests') {
            parallel {
                stage('Unit tests') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            npm test
                        '''
                    }
                    post {
                        always {
                            junit 'test-results/junit.xml'
                        }
                    }
                }

                stage('E2E Build') {
                    agent {
                        docker {
                            image 'my-playwrite'
                            reuseNode true
                        }
                    }

                    steps {
                        sh '''
                            serve -s build &
                            sleep 10
                            npx playwright test  --reporter=html
                        '''
                    }

                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Build Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }*/
// Requires Approval Step before going to deploy
        stage('Aprpoval For Stagging'){
            steps{
                timeout(time: 1, unit: 'HOURS') {
                    input 'Ready for deployment to stagging environment?'
                    }
            }
        }
// Deploying to stage
        stage('Stag Deploy'){
            agent{
                docker{
                    image 'my-playwrite'
                    reuseNode true
                }
            }
            steps{
                sh '''
                netlify --version
                echo "Deploying to stagging. Site ID: $NETLIFY_SITE_ID"
                netlify status
                # it will pass the output to json file
                netlify deploy --dir=build --json > deploy-output.json 
                node-jq -r '.deploy_url' deploy-output.json 
                '''
                script{
                    env.URL = sh(script: "node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
                }
            }
            // To pass the output to another stage we need to use script
            
        }
//E2E testing for stage

        stage('E2E Stage') {
            environment{
                CI_ENVIRONMENT_URL = "${env.URL}"
            }
            agent {
                docker {
                    image 'my-playwrite'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    npx playwright test  --reporter=html
                '''
            }

            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Stage Report', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }

//Approval before prod deployment
        stage('Aprpoval For Prod'){
            steps{
                timeout(time: 1, unit: 'HOURS') {
                    input 'Ready for deployment to Prod environment?'
                    }
                
            }
        }
        stage('Prod Deploy'){
            agent{
                docker{
                    image 'my-playwrite'
                    reuseNode true
                }
            }
            steps{
                sh '''
                netlify --version
                echo "Deploying to stagging. Site ID: $NETLIFY_SITE_ID"
                netlify status
                netlify deploy --dir=build --prod
                '''
            }
            
        }
//Testing after prod deployment
        stage('E2E Prod') {
            environment{
                CI_ENVIRONMENT_URL = 'https://idyllic-chaja-4b08b9.netlify.app'
            }
            agent {
                docker {
                    image 'my-playwrite'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    npx playwright test  --reporter=html
                '''
            }

            post {
                always {
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright Prod Report', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }

    }
}
