pipeline {
    agent any

    tools {
        nodejs 'nodejs-23.9.0'
    }

    environment {
        JAVA_OPTS = "-Xmx4g -Xms512m"
        MONGO_URI = "mongodb+srv://supercluster.d83jj.mongodb.net/superData"
        MONGO_DB_CREDS = credentials('mongo-db-cred')
        MONGO_USERNAME = credentials('mongo-db-usrname')
        MONGO_PASSWORD = credentials('mongo-db-pwd')

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
                            // Run npm audit and fail only if critical vulnerabilities are found
                            sh 'npm audit --audit-level=critical'
                            echo "✅ No critical vulnerabilities detected in NPM packages."
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
                            dependencyCheckPublisher failedTotalCritical: 1, pattern: 'dependency-check-report/dependency-check-reportxml', stopBuild: true

                            junit allowEmptyResults: true, keepProperties: true, testResults: 'dependency-check-report/dependency-check-junit.xml'

                            publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: 'dependency-check-report', reportFiles: 'dependency-check-jenkins.html', reportName: 'HTML Report-dependency check', reportTitles: '', useWrapperFileDirectly: true])

                        }
                    }
                }
            }
        }
        stage('Unit Testing') {
            steps {

                sh 'echo $MONGO_DB_CREDS'
                sh 'npm test'

                
                junit allowEmptyResults: true, keepProperties: true, testResults: 'test-results.xml'
            }
        }

        stage('Code coverage') {
            steps {
                
                  
                catchError(buildResult: 'SUCCESS', message: 'It will be fixed in future releases!!!!!!!!!!!!!', stageResult: 'UNSTABLE') {
  
                    sh 'npm run coverage'
                }
                publishHTML([allowMissing: true, alwaysLinkToLastBuild: true, icon: '', keepAll: true, reportDir: 'coverage/lcov-report', reportFiles: 'index.html', reportName: 'Coverage test report', reportTitles: '', useWrapperFileDirectly: true])

                
            }
        }
    }
}
