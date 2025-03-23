pipeline {
    agent any

    tools {
        nodejs 'nodejs-23.9.0'
    }

    environment {
        JAVA_OPTS = "-Xmx4g"  // Increase heap size to 4GB
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
                        withEnv(["NVD_API_KEY=550c72a8-466a-45f6-85b3-24fd0508caa5"]) {
                            sh '''
                            export JAVA_OPTS="-Xmx4g"
                            dependencyCheck --scan "./" \
                                           --out "./" \
                                           --format "ALL" \
                                           --prettyPrint \
                                           --nvdApiKey $NVD_API_KEY
                            '''
                        }
                    }
                }
            }
        }
    }
}
