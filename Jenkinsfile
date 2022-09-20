pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('cmuriuki-dockerhub')
    }
    stages {
        stage('Cloning our Git') {
            steps {
                git credentialsId: 'GIT_HUB_CREDENTIALS', url: 'https://github.com/albertwayland/within3-weather-station.git', branch: 'master'
            }
        }
        stage('Docker Build and Tag') {
            steps {
                sh 'docker build -t docker build --tag node-docker:latest .'
                sh 'docker tag node-docker rwayland/node-docker:latest'
                sh 'docker tag node-docker rwayland/node-docker:$BUILD_NUMBER'
            }
        }

        stage('Publish image to Docker Hub') {
            steps {
                sh  'echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin '
                sh  'docker push rwayland/node-docker:latest'
                sh  'docker push rwayland/node-docker:$BUILD_NUMBER'
            }
        }
        stage('Deploy to EC2') {
            steps {
                sshagent(['nodeja-app']) {
                    sh 'scp -o StrictHostKeyChecking=no ./ ubuntu@ec2-184-73-57-135.compute-1.amazonaws.com:/home/ubuntu/within3-weather-station'
                    sh 'ssh ubuntu@ec2-184-73-57-135.compute-1.amazonaws.com sudo apt -y update && sudo apt install -y nodejs npm'
                    sh 'ssh ubuntu@ec2-184-73-57-135.compute-1.amazonaws.com sudo npm install express'
                    sh 'ssh ubuntu@ec2-184-73-57-135.compute-1.amazonaws.com node index.js'
                }
            }
        }
    }
}
