pipeline {
    agent any

    //Setting up environment varibales
    environment {
        NETLIFY_SITE_ID = 'ccfcc967-b439-4a2a-b7a2-3ff22e577fdc'
        NETLIFY_AUTH_TOKEN = credentials('netlify-jenkins')
        REACT_APP_VERSION = "1.0.${BUILD_ID}"
        AWS_DEFAULT_REGION = 'ap-south-1'
        APP_NAME= 'myjenkinsapp'
        AWS_ECR = '013046900819.dkr.ecr.ap-south-1.amazonaws.com'
    }

    // Starting the pipeline with stages
    stages {

        //Building the application
        stage('Build') {
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
    //Deploying to AWS S3
        /*stage('AWS') {
            agent {
                docker {
                    image 'my-awscli'
                    args "--entrypoint=''"
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'aws', passwordVariable: 'AWS_SECRET_ACCESS_KEY', usernameVariable: 'AWS_ACCESS_KEY_ID')]) {
                    sh '''
                    aws --version
                    # echo "Hello World!" > index.html
                    # aws s3 sync build s3://jenkins-test-rishabh-bucket
                    LATEST_TD_VERSION=$(aws ecs register-task-definition --cli-input-json file://aws/task-definition.json | jq '.taskDefinition.revision')
                    aws ecs update-service --cluster JenkinsApp --service JenkinsApp-container-task-service-2u9sddzw --task-definition JenkinsApp-container-task:$LATEST_TD_VERSION
                    aws ecs wait services-stable --cluster JenkinsApp --service JenkinsApp-container-task-service-2u9sddzw
                    '''
                    // some block
                }
            }
        }*/
        stage('Docker Image'){
            agent{
                docker{
                    image 'amazon/aws-cli'
                    reuseNode true
                    args "-u root -v /var/run/docker.sock:/var/run/docker.sock --entrypoint=''"
                }
            }
            steps{
                sh '''
                    amazon-linux-extras install docker
                    docker build -t $AWS_ECR/$APP_NAME:$REACT_APP_VERSION .
                    aws ecr get-login-passowrd | docker login --username AWS --password-stdin $AWS_ECR
                    docker push $AWS_ECR/$APP_NAME:$REACT_APP_VERSION
                '''
            }
        }

        //Testing the Build: Parallel testing of Unit and E2E testing
        /*stage('Tests') {
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
        /*stage('Aprpoval For Stagging') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    input 'Ready for deployment to stagging environment?'
                }
            }
        }*/

        // Deploying to stage
        /*stage('Stag Deploy') {
            agent {
                docker {
                    image 'my-playwrite'
                    reuseNode true
                }
            }
            steps {
                sh '''
                netlify --version
                echo "Deploying to stagging. Site ID: $NETLIFY_SITE_ID"
                netlify status
                # it will pass the output to json file
                netlify deploy --dir=build --json > deploy-output.json 
                node-jq -r '.deploy_url' deploy-output.json 
                '''
                script {
                    env.URL = sh(script: "node-jq -r '.deploy_url' deploy-output.json", returnStdout: true)
                }   
            }
            // To pass the output to another stage we need to use script
        }*/

        //E2E testing for stage
        /*stage('E2E Stage') {
            environment {
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
        }*/

        //Approval before prod deployment
        /*stage('Aprpoval For Prod') {
            steps {
                timeout(time: 1, unit: 'HOURS') {
                    input 'Ready for deployment to Prod environment?'
                }
            }
        }*/

        /*stage('Prod Deploy') {
            agent {
                docker {
                    image 'my-playwrite'
                    reuseNode true
                }
            }
            steps {
                sh '''
                netlify --version
                echo "Deploying to stagging. Site ID: $NETLIFY_SITE_ID"
                netlify status
                netlify deploy --dir=build --prod
                '''
            }
        }*/

        //Testing after prod deployment
        /*stage('E2E Prod') {
            environment {
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
        }*/

    }
}
