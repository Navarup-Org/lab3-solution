pipeline {
    agent any

    tools {
        nodejs 'nodejs-23.9.0'
    }

    stages {
        stage('Installing dependencies') {
            steps {
                sh 'npm install --no-audit'
            }
        }
        stage('Dependencies check') {
            parallel {
                stage('Npm audit check') {
                    steps {
                        sh '''
                        npm audit --audit-level=critical
                        echo $?
                        '''
                    }
                }
                stage('Owasp dependency check') {
                    steps {
                        withEnv(["NVD_API_KEY=${credentials('NVD_API_KEY')}"]) {
                            dependencyCheck additionalArguments: '''
                            --scan "./"
                            --out "./"
                            --format "ALL"
                            --prettyPrint
                            --nvdApiKey $NVD_API_KEY
                            ''', odcInstallation: 'OWASP-CHECK'
                        }
                    }
                }
            }
        }
    }
}
