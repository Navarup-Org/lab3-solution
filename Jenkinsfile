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

        stage('Security Checks') {
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
                            --noupdate
                            --disableYarnAudit
                            ''', odcInstallation: 'OWASP-CHECK'
                            dependencyCheckPublisher failedTotalCritical: 1, pattern: 'dependency-check-report/dependency-check-report.xml', stopBuild: true

                        }
                    }
                }
            }
        }
    }
}
