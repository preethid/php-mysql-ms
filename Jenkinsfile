pipeline{
    agent none

    environment{
        BUILD_SERVER='ec2-user@172.31.12.47'
        IMAGE_NAME='devopstrainer/java-mvn-privaterepos:php$BUILD_NUMBER'
        DEPLOY_SERVER='ec2-user@172.31.11.114'
    }
    stages{
        stage('Build the php application'){
            agent any
            steps{
                script{
                    sshagent(['slave2']){
                         withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                        echo "Containerizing the application"
                        sh "scp -o StrictHostKeyChecking=no -r BuildConfig ${BUILD_SERVER}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${BUILD_SERVER} 'bash /home/ec2-user/BuildConfig/docker-script.sh'"
                        sh "ssh ${BUILD_SERVER} sudo docker build -t ${IMAGE_NAME} /home/ec2-user/BuildConfig/"
                        sh "ssh ${BUILD_SERVER} sudo docker login -u ${USERNAME} -p ${PASSWORD}"
                        sh "ssh ${BUILD_SERVER} sudo docker push ${IMAGE_NAME}"
                         }
                    }
                }
            }
        }
        stage('Deploy the php application'){
            agent any
            steps{
                script{
                    sshagent(['slave2']){
                         withCredentials([usernamePassword(credentialsId: 'docker-hub', passwordVariable: 'PASSWORD', usernameVariable: 'USERNAME')]) {
                        echo "Containerizing the application"
                        sh "scp -o StrictHostKeyChecking=no -r DeployConfig ${DEPLOY_SERVER}:/home/ec2-user"
                        sh "ssh -o StrictHostKeyChecking=no ${DEPLOY_SERVER} 'bash /home/ec2-user/DeployConfig/docker-script.sh'"
                       // sh "ssh ${DEPLOY_SERVER} sudo docker build -t ${IMAGE_NAME} /home/ec2-user/BuildConfig/"
                        sh "ssh ${DEPLOY_SERVER} sudo docker login -u ${USERNAME} -p ${PASSWORD}"
                      //  sh "ssh ${DEPLOY_SERVER} sudo docker run -itd -P ${IMAGE_NAME}"
                      sh "ssh ${DEPLOY_SERVER} bash /home/ec2-user/DeployConfig/docker-compose-script.sh ${IMAGE_NAME}"
                         }
                    }
                }
            }
        }
    }
}