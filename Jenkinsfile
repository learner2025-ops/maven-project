pipeline{
    agent any
    tools{
        maven 'maven'
    }
//     parameters {
//   choice choices: ['dev', 'prod'], name: 'select_environment'
// }
environment {
        DEV_SERVER = "35.175.204.225"
        PROD_SERVER = "184.73.10.171"
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
        when { branch 'feature' beforeAgent true}
         agent any
        steps{
            sshagent(credentials: ['remote_ssh_login']) {
                    sh '''
                    echo "Copy WAR to remote server"
                    scp -o StrictHostKeyChecking=no \
                    /var/lib/jenkins/workspace/test_multi/webapp/target/*.war \
                    ${REMOTE_USER}@${REMOTE_SERVER}:/tmp/${APP_NAME}.war

                    echo "Deploy application"

                    ssh -o StrictHostKeyChecking=no \
                    ${REMOTE_USER}@${REMOTE_SERVER} "

                        rm -rf ${TOMCAT_PATH}/${APP_NAME}*

                        mv /tmp/${APP_NAME}.war \
                        ${TOMCAT_PATH}/${APP_NAME}.war
                        cd /var/www/html/
                        jar -xvf webapp.war
                        sudo systemctl restart apache2
                    "
                    '''
                }
        }
    }
    stage("deploy_prod")
    {
        // when{ expression {params.select_environment == 'dev'}
        // beforeAgent true}
        // agent { label 'dev'}
        when { branch 'master' beforeAgent true}
         agent any
        steps{
            sshagent(credentials: ['remote_ssh_login']) {
                    sh '''
                    echo "Copy WAR to remote server"
                    scp -o StrictHostKeyChecking=no \
                    /var/lib/jenkins/workspace/test_multi/webapp/target/*.war \
                    ${REMOTE_USER}@${REMOTE_SERVER}:/tmp/${APP_NAME}.war

                    echo "Deploy application"

                    ssh -o StrictHostKeyChecking=no \
                    ${REMOTE_USER}@${REMOTE_SERVER} "

                        rm -rf ${TOMCAT_PATH}/${APP_NAME}*

                        mv /tmp/${APP_NAME}.war \
                        ${TOMCAT_PATH}/${APP_NAME}.war
                        cd /var/www/html/
                        jar -xvf webapp.war
                        sudo systemctl restart apache2
                    "
                    '''
                }
        }
    }
    }
}


