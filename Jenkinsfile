pipeline {
    agent any
    environment{
        NETLIFY_SITE_ID='29e0e32d-2e63-41da-bbc7-90d59ee43015'
        NETLIFY_AUTH_TOKEN=credentials("netlify-token")
    }

    stages {
        // stage('Build') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         echo 'starting build'
        //         sh'''
        //           npm --version
        //           node --version
        //           ls -la
        //           npm ci
        //           npm run build
        //           ls -la
        //         '''
        //     }
        // }
        stage("parallel-test"){
            parallel{
                 stage("Unit-Test"){
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
                post{
                        always {
                            junit 'jest-results/junit.xml'
                            
                        }
                    }
        }
        stage("E2E"){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            steps{
                echo "running playright test"
                sh '''
                  npm install serve
                  node_modules/.bin/serve -s build  &
                  sleep 10
                  npx playwright test --reporter=html
                '''
            }
            post{
                    always {
                        
                        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
        }


            }
        }

        stage('Deploy') {
            agent {
                docker {
                    image 'node:18-alpine'
                    reuseNode true
                }
            }
            steps {
                echo 'starting deploy'
                sh'''
                  npm install netlify-cli@20.1.1
                  node_modules/.bin/netlify --version
                  echo "deploying to site with ID $NETLIFY_SITE_ID"
                  node_modules/.bin/netlify status
                  node_modules/.bin/netlify deploy --dir=build --prod


                '''
            }
        }

         stage("E2E prod"){
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.39.0-jammy'
                    reuseNode true
                }
            }
            environment{
                CI_ENVIRONMENT_URL="https://eloquent-beijinho-e6248e.netlify.app/"
            }
            steps{
                echo "running playright test"
                sh '''
                 
                  npx playwright test --reporter=html
                '''
            }
            post{
                    always {
                        
                        publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, icon: '', keepAll: false, reportDir: 'playwright-report', reportFiles: 'index.html', reportName: 'HTML e2e Report', reportTitles: '', useWrapperFileDirectly: true])
                    }
                }
        }

       
    }

    
}
