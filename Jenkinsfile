pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:alpine'
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
                    ls -la
                '''
            }
        }
        
        stage('Test') {  // This stage should not be nested inside the Build stage
            
             agent {
                docker {
                    image 'node:alpine'
                    reuseNode true
                }
            }

            steps {
                sh '''
                    test -f build/index.html
                    npm test
                    '''
            }
        }
    }

    post{
        always {
            junit 'test-results/junit.xml'
        }
    }
}
