pipeline {
    agent any
    
    options {
        ansiColor('xterm')
    }

    stages {
        stage('build') {
            agent {
                docker {
                    image 'node:22-alpine'
                }
            }
            steps {
                sh 'npm ci'
                sh 'npm run build'
            }
        }

        stage('test') {
            parallel {
                stage('unit tests') {
                    agent {
                        docker {
                            image 'node:22-alpine'
                            reuseNode true
                        }
                    }
                    steps {
                        // Unit tests with Vitest
                        sh 'npm ci' // ensure vitest and dependencies are installed in this container
                        sh 'npx vitest run --reporter=verbose'
                    }
                }
                 stage('integration tests') {
                    agent {
                        docker {
                            image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                            reuseNode false
                        }
                    }
                    steps {
                        sh 'npx playwright test'
                    }
                }
            }
        }

        stage('deploy') {
            agent {
                docker {
                    image 'alpine'
                }
            }
            steps {
                // Mock deployment which does nothing
                echo 'Mock deployment was successful!'
            }
        }

        stage('e2e') {
            agent {
                docker {
                    image 'mcr.microsoft.com/playwright:v1.54.2-jammy'
                    reuseNode false
                }
            }
            environment {                
                 E2E_BASE_URL = 'https://spanish-cards.netlify.app/'
            }
            steps {
                sh 'npx playwright test'
            }
             post {
        always {
            publishHTML(target: [
                allowMissing: false,
                alwaysLinkToLastBuild: true,
                keepAll: true,
                reportDir: 'reports/html-output', // Path to the directory containing your report
                reportFiles: 'index.html',         // The main file of your report
                reportName: 'Test Report'          // Name displayed on the Jenkins sidebar
            ])
        }
    }
        }
    }
}