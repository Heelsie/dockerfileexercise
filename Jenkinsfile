pipeline {

    agent any

    stages {

         stage('Init') {

            steps {

                sh 'docker rm -f $(docker ps -qa) || true'

                sh 'docker network create new-network || true'

            }

        }

        stage('Build') {

            steps {

                sh 'docker build -t flask-app .'

                sh 'docker build -t mynginx -f nginx/Dockerfile .'

            }

        }

        stage('Deploy') {

            steps {

                sh 'docker run -d --name flask-app --network new-network flask-app:latest'

                sh 'docker run -d -p 80:80 --name mynginx --network new-network mynginx:latest'

            }

        }

    }

}