pipeline {
    agent any

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Maven Project') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('Sonarube') {
                    withCredentials([ 
                        string( 
                            credentialsId: 'sonarqube-token', 
                            variable: 'SONAR_TOKEN' 
                            ) 
                    ]) {
                    sh '''
                        mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar \
                          -Dsonar.projectKey=webhook-test \
                          -Dsonar.token="$SONAR_TOKEN"
                    '''
                }
            }
        }

        stage('Wait for Quality Gate') {
            steps {
                timeout(time: 5, unit: 'MINUTES') {
                    waitForQualityGate abortPipeline: true
                }
            }
        }
    }
}