pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:alpine'
                    args '-u root'  // Run as root user
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
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                    args '-u root:root'
                }
            }

            steps {
                sh '''
                   npm install serve
                   node_modules/.bin/serve -s build &
                   sleep 10
                   npx playwright test --reporter=html

                    '''
            }
        }
    }

    post{
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
