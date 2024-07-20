pipeline {
    agent {
        docker {
            image 'node:18-alpine'
            args '-u root'
        }
    }
    stages {
        stage('Install Dependencies') {
            steps {
                sh 'npm ci'
            }
        }
        stage('Build') {
            steps {
                sh 'npm run build'
            }
        }
        stage('Test') {
            steps {
                echo 'Test stage'
                sh 'test -f build/index.html || (echo "index.html not found" && exit 1)'
            }
        }
    }
}