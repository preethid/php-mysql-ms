pipeline{
    agent none

    environment{
        BUILD_SERVER='ec2-user@172.31.12.47'
        IMAGE_NAME='devopstrainer/phpapp:php{BUILD_NUMBER}'
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
                        sh "ssh ${BUILD_SERVER} 'sudo docker build -t ${IMAGE_NAME} /home/ec2-user/BuildConfig/'"
                        sh "ssh ${BUILD_SERVER} 'sudo docker login -u ${USERNAME} -p ${PASSWORD}'"
                        sh "ssh ${BUILD_SERVER} 'sudo docker push ${IMAGE_NAME}'"
                         }
                    }
                }
            }
        }
    }
}