pipeline {
    agent any
    tools {
        maven "Maven"   
    }   
    /*environment{
        sonarscanner = tool 'SonarScanner'
    }*/
    stages {
        stage('Compile-Build-Test ') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('SonarQube Analysis')
        {
             environment {
                scannerHome=tool 'SonarScanner'
            }
             steps{
                withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId:'sonar_credentials', usernameVariable: 'USER', passwordVariable: 'PASS']])
                 {
                     sh "mvn $USER:$PASS -Dsonar.host.url=http://3.16.33.107:9000"
                 }
             }
         }
        /*stage('SonarQube Analysis'){
            steps{
               withSonarQubeEnv('sonarqube'){
                     sh '${sonarscanner}/bin/sonar-scanner -Dproject.settings=./sonar-project.properties'
                }
            }
        }*/
        stage("Quality Gate") {
            steps {
              timeout(time: 3, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
         stage("collecting") {
            steps {
                sh 'curl -X GET -v -G http://3.16.33.107:9000/api/issues/search?componentRoots=org.codehaus.sonar:sonar'
              //sh 'curl -X GET -v -u admin:admin http://3.16.33.107:9000/api/issues/set_severity?issue=ac539576-df80-4a1f-9631-991b31ad27ef&severity=BLOCKER'
              
            }
        }
        /*stage('Pushing to Nexus'){
            steps{
               withCredentials([usernamePassword(credentialsId: 'nexus-credentialss', passwordVariable: 'password', usernameVariable: 'username'),string(credentialsId: 'NEXUS_URL', variable: 'nexus_url')]){
                    //sh 'curl -u ${username}:${password} --upload-file target/BMIBeta-${BUILD_NUMBER}.jar http://18.224.155.110:8081/nexus/content/repositories/devopstraining/comrades/BMI-${BUILD_NUMBER}.war'
                   sh 'curl -v -F file=@target/BMI-0.war -u ${username}:${password} http://${nexus_url}/nexus/content/repositories/devopstraining/comrades/bmi/BMI/BMI-${BUILD_NUMBER}.war'
               }
            }
        }
        stage('Uploading playbook to Ansible'){
            steps{
                withCredentials([string(credentialsId: 'ANSADMIN_PASSWORD', variable: 'ansadmin_password')]){
                //sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//playbooks', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'project-ansible.yml')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])   
                sh 'sshpass -p ${ansadmin_password} scp -v BMI-Playbook.yml ansadmin@172.31.22.13:/opt/playbooks'
                }
            }
            
        }
        stage('Uploading artifacts to Ansible'){
            steps{
                withCredentials([string(credentialsId: 'ANSADMIN_PASSWORD', variable: 'ansadmin_password')]){
                //sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/playbooks/project-ansible.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '//opt//playbooks', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '/target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
                //sh 'scp -v -i /home/ansadmin/.ssh/jen-ans.pub -o StrictHostKeyChecking=no target/*.war ansadmin@172.31.22.13:/opt/playbooks/target'
                //sh 'sshpass -p ${ansadmin_password} scp -v target/*.war ansadmin@172.31.22.13:/opt/playbooks/target'
                sh 'sshpass -p ${ansadmin_password} ssh -v -o StrictHostKeyChecking=no ansadmin@172.31.22.13 \"cd /opt/playbooks/target; wget -O BMI.war http://18.224.155.110:8081/nexus/content/repositories/devopstraining/comrades/bmi/BMI/BMI-${BUILD_NUMBER}.war\"'
                }
            }
        }
       stage('Executing Playbook'){
            steps{
                withCredentials([string(credentialsId: 'ANSADMIN_PASSWORD', variable: 'ansadmin_password')]){//-C \"export VAULTPASS=${ansadmin_password};
                    sh 'sshpass -p ${ansadmin_password} ssh -v -o StrictHostKeyChecking=no ansadmin@172.31.22.13  \"ansible-playbook /opt/playbooks/BMI-Playbook.yml\"'
                    //sshPublisher(publishers: [sshPublisherDesc(configName: 'Ansible_server', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: 'ansible-playbook /opt/playbooks/project-ansible.yml', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: '', remoteDirectorySDF: false, removePrefix: '', sourceFiles: '')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: true)])
                }
            }
        }
        
        /*stage('Deployment to AWS'){
            steps{
            withCredentials([usernamePassword(credentialsId: 'tomcatCredentials', passwordVariable: 'password', usernameVariable: 'username'),string(credentialsId: 'TOMCAT_URL', variable: 'tomcat_url')]){
                    sh 'curl ${tomcat_url}/manager/text/undeploy?path=/BMI -u ${username}:${password}'
                    sh 'curl -v -u ${username}:${password} -T target/BMI${BUILD_NUMBER}.war ${tomcat_url}/manager/text/deploy?path=/BMI'
                }
            }
        }*/
        
     }
              /* post { 
                success { 
                    echo 'notified to slack '
                    slackSend (color: '#00FF00', message: " JOB SUCCESSFUL: Job '${JOB_NAME} [${BUILD_NUMBER}]' (${BUILD_URL})")
                }
                failure {
                    echo 'notified to slack'
                    slackSend (color: '#FF0000', message: " JOB FAILED: Job '${JOB_NAME} [${BUILD_NUMBER}]' (${BUILD_URL})")
                }
               }*/
   

}
