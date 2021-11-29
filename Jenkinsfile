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
                    withSonarQubeEnv('SonarQube') {    //名字与Jenkins配置中相同
                        def project_key="api-test-demo"
                        def scannerHome = tool 'sonar-scanner';  //名字与Jenkins配置中相同
                        sh "${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=${project_key} -Dsonar.projectName=${project_key} -Dsonar.java.binaries=build/classes"
                    }
                }
            }
        }

        stage('Build jar'){
            steps{
                sh "./gradlew -x jar build"
            }
        }

        stage('Build image') {
            steps{
                sh "docker build -t api-demo:v${env.BUILD_NUMBER} ."
            }
        }

        stage('Deploy DEV') {
            steps{
                sh 'docker ps -f name=api-container-dev -q  | xargs --no-run-if-empty docker rm -f'
                sh "docker run -d --name api-container-dev -p 8080:8080 api-demo:v${env.BUILD_NUMBER}"
            }
        }

        stage('Api Test'){
            steps{
                sh 'sleep 30'
                sh '/opt/apache-jmeter-5.4.1/bin/jmeter.sh -n -t api-test/api-test-demo.jmx'
            }
        }
    }
}

