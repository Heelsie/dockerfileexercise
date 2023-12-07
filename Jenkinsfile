pipeline {

    agent any

    enironment {
        // Declare variable to set environment
        WorkingEnv = "undetermined"
    }

    stages {

         stage('Init') {

            steps {
                //echo "You have selected $(GIT_BRANCH) GIT Branch"
                if (env.GIT_BRANCH =='origin/main') {

                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.25  << EOF
                docker rm -f $(docker ps -qa) || true
                docker network create new-network || true
                '''
                }
                else if (env.GIT_BRANCH == 'origin/test') {
                    sh '''
                ssh -i ~/.ssh/id_rsa jenkins@TestIP  << EOF
                docker rm -f $(docker ps -qa) || true
                docker network create new-network || true
                '''
                }

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

                sh '''
                ssh -i ~/.ssh/id_rsa jenkins@10.154.0.25  << EOF
                docker run -d --name flask-app --network new-network heelsie/flask-jenk:latest
                docker run -d -p 80:80 --name mynginx --network new-network heelsie/nginx-jenk:latest
                '''

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