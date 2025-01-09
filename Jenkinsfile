pipeline{
    agent any

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
        stage('Test'){
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
                image 'mcr.microsoft.com/playwright:v1.49.1-noble'
                reuseNode true
            }
            steps{
                sh '''
                    echo "-----------------------E2E---------------------------"
                    npm install -g serve
                    serve -s build
                '''
            }
        }
    }
    post{
        always{
            junit 'test-results/junit.xml'
        }
    }
}