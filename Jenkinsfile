pipeline {
    agent any

        environment {
           EC2_SSH_USER = 'ubuntu'  // or your EC2 instance username
           IP = '18.183.240.86'
         }

    stages {
        stage('Git Clone') {
            steps {
                echo 'Cloning Git repository...'
                checkout scmGit(branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github_credentials', url: 'https://github.com/narharigtm/appimg.git']])
            }
        }

        stage('Build Docker Image') {
            steps {
                echo 'Building Docker image...'
                script {
                    sh '''
                        sudo docker build -t dollimg:v4 .
                        sudo docker images
                        sudo docker tag dollimg:v4 narhari/mero-dollimg:v5
                    '''
                }
            }
        }

        stage('Push Docker Image to Registry') {
            steps {
                echo 'Pushing Docker image to Docker registry...'
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker_hub_cre', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh '''
                        echo $DOCKER_PASSWORD | sudo docker login -u $DOCKER_USERNAME --password-stdin
                        sudo docker push narhari/mero-dollimg:v5
                    '''
}
                    }
                }
            }
       """
        stage('deployed to dev server ') {
            steps{
                sshagent(credentials:['id-aws']){
                  sh 'ssh  -o StrictHostKeyChecking=no ${EC2_SSH_USER}@${IP} ./deploy.sh'
                  
                  }
              }
          }
            
        
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
