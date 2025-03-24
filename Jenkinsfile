pipeline {
    agent any

    tools {
        nodejs 'nodejs-23.9.0'
    }

    environment {
        JAVA_OPTS = "-Xmx4g -Xms512m"
        NVD_API_KEY = "550c72a8-466a-45f6-85b3-24fd0508caa5"
    }

    stages {
        stage('Installing dependencies') {
            steps {
                sh 'npm install --no-audit'
            }
        }

        stage('Purge OWASP Dependency-Check DB') {
            steps {
                sh 'rm -rf ~/.dependency-check/data/* || true'
            }
        }

        stage('Dependencies check') {
            parallel {
                stage('Npm audit check') {
                    steps {
                        script {
                            def auditResult = sh(script: 'npm audit --json', returnStdout: true).trim()
                            if (auditResult.contains('"critical"')) {
                                error("❌ Critical vulnerabilities found in NPM dependencies!")
                            } else {
                                echo "✅ No critical vulnerabilities detected in NPM packages."
                            }
                        }
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
                            --disableCvssV4
                            --nvdApiKey $NVD_API_KEY
                            ''', odcInstallation: 'OWASP-CHECK'
                        }
                    }
                }
            }
        }
    }
}
