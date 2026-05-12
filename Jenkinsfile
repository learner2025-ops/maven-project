pipeline{
    agent any
    tools{
        maven 'maven'
    }
//     parameters {
//   choice choices: ['dev', 'prod'], name: 'select_environment'
// }
environment {
        REMOTE_SERVER = "3.81.74.39"
        REMOTE_USER   = "ubuntu"
        TOMCAT_PATH   = "/var/www/html"
        APP_NAME      = "webapp"
    }
    stages{
        stage('Build'){
            steps{
              sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('test'){
        parallel{
            stage('testA'){
                agent any
                steps{
                    echo 'this is test A'
                    sh 'mvn test'
                }
            }
            stage('testB'){
                agent any
                steps{
                    echo 'this is test B'
                    sh 'mvn test'
                }
            }
        }
        // post {
        //     success {
        //         // dir("webapp/target/")
        //         // {
        //         //     stash name: "maven-build", includes: "*.war"
        //         // }
        //             //archiveArtifacts artifacts: '**/target/*.war'
        //         }         
        //     }
    }
    stage("deploy_dev")
    {
        // when{ expression {params.select_environment == 'dev'}
        // beforeAgent true}
        // agent { label 'dev'}
         agent any
        steps{
            
            sshagent(credentials: ['remote_ssh_login']) {
                    sh '''
                    echo "Copy WAR to remote server"
                    cd ${WORKSPACE}
                    cd ..
                    scp -o StrictHostKeyChecking=no \
                    deployment/webapp/target/*.war \
                    ${REMOTE_USER}@${REMOTE_SERVER}:/tmp/${APP_NAME}.war

                    echo "Deploy application"

                    ssh -o StrictHostKeyChecking=no \
                    ${REMOTE_USER}@${REMOTE_SERVER} "

                        rm -rf ${TOMCAT_PATH}/${APP_NAME}*

                        mv /tmp/${APP_NAME}.war \
                        ${TOMCAT_PATH}/${APP_NAME}.war
                        cd /var/www/html/
                        jar -xvf webapp.jar
                        systemctl restart apache2
                    "
                    '''
                }
        }
    }
    }
}


