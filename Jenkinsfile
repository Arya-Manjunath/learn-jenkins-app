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

         stage('E2E') {  // This stage should not be nested inside the Build stage
            
             agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.46.0-jammy'
                    reuseNode true
                }
            }

            steps {
                sh '''
                   npm install -g serve
                   serve -s build
                   npx playwright test

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
