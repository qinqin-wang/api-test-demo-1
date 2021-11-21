//declarative pipeline
pipeline {
    agent any

    stages {
        stage('Unit Test'){
            steps{
                sh "echo  I am stage: Unit Test"
                sh "pwd"
                sh "./gradlew clean build"
            }
        }

        stage('Sonar Scan') {
            steps {
                script {
                    withSonarQubeEnv('SonarQube') {
                        def project_key="api-test-demo"
                        def scannerHome = tool 'sonar-scanner';
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=${project_key} -Dsonar.projectName=${project_key}"
                    }
                }
            }
        }

        stage('Build jar'){
            steps{
                sh "./gradlew -x jar build"
            }
        }

        stage('\u270D Build image') {
            steps{
                sh "docker build -t api-demo:v${env.BUILD_NUMBER} ."
            }
        }

        stage('\u26F1 Deploy') {
            steps{
                sh 'docker ps -f name=api-container -q  | xargs --no-run-if-empty docker rm -f'
                sh "docker run -d --name api-container -p 8080:8080 api-demo:v${env.BUILD_NUMBER}"
            }
        }

        stage('\u261D Api Test'){
            steps{
                sh 'sleep 30'
                // build job: 'Jmeter-test'
                sh '/opt/apache-jmeter-5.4.1/bin/jmeter.sh -n -t api-test/api-test-demo.jmx'
            }
        }
    }
}
