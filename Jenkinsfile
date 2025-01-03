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
                mkdir build
                touch build/index.html
                echo "MainBoard" >> build/index.html
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
}