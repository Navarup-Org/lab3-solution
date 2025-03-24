pipeline {
    agent any

    tools {
        nodejs 'nodejs-23.9.0'
    }

    environment {
        JAVA_OPTS = "-Xmx4g -Xms512m"
    }

    stages {
        stage('Installing dependencies') {
            steps {
                sh 'npm install --no-audit'
            }
        }

        stage('Owasp dependency check') {
            steps {
                withEnv(["JAVA_OPTS=-Xmx4g -Xms512m"]) {
                    dependencyCheck additionalArguments: '''
                    --scan "./"
                    --out "./dependency-check-report"
                    --format "ALL"
                    --prettyPrint
                    --failOnCVSS 7
                    --noupdate
                    ''', odcInstallation: 'OWASP-CHECK'
                }
            }
        }
    }
}
