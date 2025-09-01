pipeline {
    agent any

    stages {
        // This is a comment
        stage('Build') {
            agent {
                docker {
                    image 'node:18-alpine'
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

        stage ('Run Tests')
        {
            parallel {
                stage("Test1"){
                    agent {
                        docker {
                            image 'node:18-alpine'
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

                stage("Test2"){
                    steps {
                        sh '''
                        wc -l
                        '''
                    }
                }
            }
        }



    }

    post{
        always {
            junit 'jest-results/junit.xml'
        }
    }
}
