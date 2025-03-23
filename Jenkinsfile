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
                        withEnv(["NVD_API_KEY=550c72a8-466a-45f6-85b3-24fd0508caa5"]) {
                            sh '''
                            sudo -u jenkins /var/lib/jenkins/tools/org.jenkinsci.plugins.DependencyCheck.tools.DependencyCheckInstallation/OWASP-CHECK/bin/dependency-check.sh \
                            --scan "./" \
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
