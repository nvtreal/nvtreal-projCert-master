pipeline {
    agent {
        label 'Jenkins-slave'
    }

    stages {
        stage('Check  out Sourcecode') {
            steps {
                deleteDir()
                checkout scmGit(branches: [[name: 'main']], extensions: [], userRemoteConfigs: [[credentialsId: 'github', url: 'https://github.com/nvtreal/projCert-master.git']])
            }
        }

        stage('Build Images') {
            steps {
                sh '''
                cd client
                sudo docker build -t nvtreal/webphp:latest .
                '''
            }
        }  
        
        stage('Push Images to Docker Hub') {
            steps {
                sh 'sudo docker tag nvtreal/webphp:latest nvtreal/webphp:v${BUILD_NUMBER}'
                sh 'sudo docker login -u nvtreal -p abcd12345'
                sh 'sudo docker push nvtreal/webphp:v${BUILD_NUMBER}'
            }
        }   
        
        stage('Clean Docker Images Local') {
            steps {
                sh 'sudo docker stop nvtreal/webphp'
                sh 'sudo docker rm nvtreal/webphp'
                sh 'sudo docker rmi nvtreal/webphp:v${BUILD_NUMBER}'
                sh 'sudo docker rmi nvtreal/webphp:latest'
            }
        }  

        stage('Deploy Images') {
            steps {
                sh 'sudo docker run -d -it --name nvtreal/webphp -p 80:80 nvtreal/webphp:v${BUILD_NUMBER}'
            }
        }  

    }
}
