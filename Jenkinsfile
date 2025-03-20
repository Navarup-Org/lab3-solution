pipeline{
    agent any

    tools{
        nodejs 'nodejs-23.9.0'
    }

    stages{

        stage('Installing dependencies'){
            steps{
                sh 'npm install --no-audit'
            }
        }
        stage('Dependencies check'){
            parallel{
                stage('Npm audit check'){
                    steps{
                        sh'''
                        npm audit --audit-level=critical
                        echo$?
                        '''
                    }
                }
                stage('Owasp dependency check'){
                    steps{
                        dependencyCheck additionalArguments: '''
                        --scan "./"
                        --out "./"
                        --format "ALL"
                        --prettyPrint
                        ''', odcInstallation: 'OWASP-CHECK'

                    }
                }
            }
        }
        
    }
}