pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = '9ce93e6c-0454-4f03-9b47-115771147ff4'
    }

    stages {
        // Uncomment the Build stage if needed
        // stage('Build') {
        //     agent {
        //         docker {
        //             image 'node:alpine'
        //             args '-u root'  // Run as root user
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //             ls -la
        //             node --version
        //             npm --version
        //             npm ci
        //             npm run build
        //             ls -la
        //         '''
        //     }
        // }

        stage('Tests') {
            parallel {
                stage('Unit Test') {
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
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                            publishHTML([
                                allowMissing: false, 
                                alwaysLinkToLastBuild: false, 
                                keepAll: false, 
                                reportDir: 'playwright-report', 
                                reportFiles: 'index.html', 
                                reportName: 'playwright HTML Report', 
                                reportTitles: '', 
                                useWrapperFileDirectly: true
                            ])
                        }
                     }
                }

                stage('E2E') {
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
                    post {
                        always {
                            junit 'jest-results/junit.xml'
                            publishHTML([
                                allowMissing: false, 
                                alwaysLinkToLastBuild: false, 
                                keepAll: false, 
                                reportDir: 'playwright-report', 
                                reportFiles: 'index.html', 
                                reportName: 'playwright HTML Report', 
                                reportTitles: '', 
                                useWrapperFileDirectly: true
                            ])
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:alpine'
                    args '-u root'  // Run as root user
                    reuseNode true
                }
            }
            steps {
                sh '''
                   npm install netlify-cli -g
                   node_modules/.bin/netlify --version
                   echo "deploying to producion site ID :$NETLIFY_SITE_ID"
                '''
            }
        }
    }

    
}
