pipeline {
    agent any

    tools {
        nodejs 'nodejs-23.9.0'
    }

    environment {
        OWASP_CACHE = "/var/lib/jenkins/owasp-cache"
    }

    stages {

        stage('Prepare Cache Directory') {
            steps {
                sh '''
                if [ ! -d "$OWASP_CACHE" ]; then
                    sudo mkdir -p $OWASP_CACHE
                    sudo chown jenkins:jenkins $OWASP_CACHE
                fi
                '''
            }
        }

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
                stage('OWASP Dependency Check') {
                    steps {
                        dependencyCheck additionalArguments: '''
                        --scan "./"
                        --out "./"
                        --format "ALL"
                        --prettyPrint
                        --data $OWASP_CACHE
                        ''', odcInstallation: 'OWASP-CHECK'
                    }
                }
            }
        }
    }
}
