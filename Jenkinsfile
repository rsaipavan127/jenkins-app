pipeline {
    agent any

    stages {
       /* stage('Build') {
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
        }*/
        stage("Test"){
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps{
                echo "running test"
                sh '''
                  test -f build/index.html
                  npm test
                '''
            }
        }
        stage("E2E"){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.60.0-noble'
                    reuseNode true
                }
            }
            steps{
                echo "running playright test"
                sh '''
                  npm install serve
                  node_modules/.bin/serve -s build 
                  npm playwright test
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
