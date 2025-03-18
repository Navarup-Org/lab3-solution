pipeline{
    agent any

    tools{
        nodejs '23.9.0'
    }

    stages{

        stage('VM node version'){
            steps {
                sh '''
                node -v
                npm -v
                '''
            }
        }
    }
}