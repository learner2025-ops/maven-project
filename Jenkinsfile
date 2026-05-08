pipeline{
    agent any
    tools{
        maven 'maven'
    }
    stages{
        stage('Build'){
            steps{
              sh 'mvn clean package'
            }
        }
        stage('test'){
        parallel{
            stage('testA'){
                steps{
                    echo 'this is test A'
                }
            }
            stage('testB'){
                steps{
                    echo 'this is test B'
                }
            }
        }
        post {
            success {
                    archiveArtifacts artifacts: '**/target/*.war'
                }         
            }
    }
    }
}