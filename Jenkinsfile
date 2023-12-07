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

                sh '''
                docker build -t heelsie/flask-jenk:latest -t heelsie/flask-jenk:v${BUILD_NUMBER} .
                docker build -t heelsie/nginx-jenk:latest -t heelsie/nginx-jenk:v${BUILD_NUMBER} ./nginx
                '''

            }

        }
        stage('Push') {
            steps {
                sh '''
                docker push heelsie/flask-jenk:latest
                docker push heelsie/flask-jenk:v${BUILD_NUMBER}
                docker push heelsie/nginx-jenk:latest
                docker push heelsie/nginx-jenk:v${BUILD_NUMBER}
                '''
            }
        }

        stage('Deploy') {

            steps {

                sh 'docker run -d --name flask-app --network new-network flask-app:latest'

                sh 'docker run -d -p 80:80 --name mynginx --network new-network mynginx:latest'

            }
            
        }
        stage('CleanUp') {
            steps {

                sh '''
                docker system prune -f
                '''

            }

        }

    }

}