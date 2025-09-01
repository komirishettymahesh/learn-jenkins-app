pipeline {
    agent any

    environment {
        NETLIFY_SITE_ID = 'bc80df03-5d5e-4a9b-8fd1-dbc94a4863f4'
        NETLIFY_AUTH_TOKEN = credentials('netlify-token')
        REACT_APP_VERSION = "1.0.$BUILD_ID"
    }

    stages {

        stage('Docker')
        {
            steps {
                sh 'docker build -t myplaywright .'
            }
        }

        stage('Build') {
            agent {
                docker {
                    image 'myplaywright'
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


        stage("Test"){
            agent {
                docker {
                    image 'myplaywright'
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

        stage('Staging') {
            agent {
                docker {
                    image 'myplaywright'
                    reuseNode true
                }
            }

            environment {
                CI_ENVIRONMENT_URL='STAGING_URL_TO_BE_SET'
            }

            steps {
                sh '''
                    echo "deploying to staging. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify deploy --dir=build --json > deploy-output.json
                    CI_ENVIRONMENT_URL=$(node_modules/.bin/node-jq -r '.deploy_url' deploy-output.json)
                    npx playwright test --reporter=html
                '''
            }
        } 


    
        stage('Prod') {
            agent {
                docker {
                    image 'myplaywright'
                    reuseNode true
                }
            }

            environment {
                CI_ENVIRONMENT_URL='https://illustrious-hotteok-ccee86.netlify.app'
            }

            steps {
                sh '''
                    echo "deploying to production. Site ID: $NETLIFY_SITE_ID"
                    node_modules/.bin/netlify deploy --dir=build --prod
                    npx playwright test
                '''
            }
        }        


    }
}
