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
                echo "Building file"
                ls -la
                npm --version
                npm ci
                npm run build
                ls -la
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
                echo "Test Stage ----> Jan 03"
                test -f build/index.html
                npm test
                '''
            }
        }
    }
    post{
        always{
            junit 'test/junit.xml'
        }
    }
}