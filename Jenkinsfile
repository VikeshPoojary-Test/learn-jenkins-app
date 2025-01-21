pipeline{
    agent any

    environment{
        NETLIFY_SITE_ID = 'db4c4c23-9547-444f-bf2e-c4f20e023435'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
    }

    stages{
        stage('Build'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                ls -la
                node --version
                npm --version
                npm ci
                npm run build
                '''
            }
        }
        stage('Run Test'){
            parallel{
                stage('Test'){
                    post{
                        always{
                            junit 'test-results/junit.xml'
                        }
                    }
                    agent{
                        docker{
                            image 'node:18-alpine'
                            reuseNode true
                        }
                    }
                    steps{
                        sh'''
                        test -f build/index.html
                        npm test
                        '''
                    }
                }
                stage('E2E Test'){
                    agent{
                        docker{
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            reuseNode true
                        }
                    }
                    steps{
                        sh '''
                            echo "-----------------------E2E---------------------------"
                            npm install serve
                            node_modules/.bin/serve -s build &
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post{
                        always{
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }

        stage('Deploy to staging'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "The netlify site ID is $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build
                '''
            }
        }

        stage('Approval'){
            steps{
                timeout(1) {
                    input message: 'Deploy to production', ok: 'Yes,  Deploy'
                }
            }
        }

        stage('Deploy to Prod'){
            agent{
                docker{
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                sh '''
                    npm install netlify-cli
                    node_modules/.bin/netlify --version
                    echo "The netlify site ID is $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify status
                    node_modules/.bin/netlify deploy --dir=build --prod
                '''
            }
        }
        stage('Prod E2E Test'){
            agent{
                docker{
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }

            environment{
                CI_ENVIRONMENT_URL = 'https://monumental-cannoli-455167.netlify.app'
            }

            steps{
                sh '''
                    echo "----------------------Production E2E---------------------------"
                    npx playwright test --reporter=html
                '''
            }
            
            post{
                always{
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Prod E2E HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                }
            }
        }
        
    }  
}