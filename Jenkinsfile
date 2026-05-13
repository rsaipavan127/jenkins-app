pipeline {
    agent any

    stages {
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'starting build'
                sh'''
                  npm --version
                  node --version
                  ls -la
                  npm ci
                  npm run build
                  ls -la
                '''
            }
        }
    }
}
