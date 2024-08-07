pipeline {
    agent any
    stages {
        stage('Install Dependencies') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-u root'
                }
            }
            steps {
                sh 'npm ci'
            }
        }
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    args '-u root'
                    reuseNode true
                }
            }
            steps {
                sh 'npm run build'
            }
        }
        stage('Run Tests') {
            parallel {
                stage('Test') {
                    agent {
                        docker {
                            image 'node:18-alpine'
                            args '-u root'
                            reuseNode true
                        }
                    }
                    steps {
                        echo 'Test stage'
                        sh 'test -f build/index.html || (echo "index.html not found" && exit 1)'
                        sh 'npm test'
                    }
                    post {
                        always {
                            junit 'jest-results/junit.xml'
            
                        }
                    }
                }
                stage('E2E') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                            args '-u root'
                            reuseNode true
                        }
                    }
                    steps {
                        sh '''
                            npm install -g serve
                            serve -s build & 
                            sleep 10
                            npx playwright test --reporter=html
                        '''
                    }
                    post {
                        always {
                            publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'Playwright HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                        }
                    }
                }
            }
        }
    }
}