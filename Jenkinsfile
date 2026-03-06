pipeline{
    agent {label "dev"};
    stages{
        stage("Code-Clone"){
            steps{
                git url: "https://github.com/arpanelfranklin/full-stack_chatApp.git", branch: "main"
            }
        }
        stage("Docker-Build"){
            steps{
                sh "docker build -t chat-app-frontend:v${BUILD_NUMBER} ./frontend"
                sh "docker build -t chat-app-backend:v${BUILD_NUMBER} ./backend"
            } 
        }
        stage("Docker-Push"){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubCreds",
                    username: "dockerHubUser",
                    password: "dockerHubPass"
                    )]){
                    sh "docker login -u ${env.username} -p ${env.passowrd}"
                    sh "docker image tag chat-app-frontend:v${env.BUILD_NUMBER} ${env.username}/chat-app-frontend:v${env.BUILD_NUMBER}"
                    sh "docker image tag chat-app-backend:v${env.BUILD_NUMBER} ${env.username}/chat-app-backend:v${env.BUILD_NUMBER}"
                    sh "docker push ${env.username}/chat-app-frontend:v${env.BUILD_NUMBER}"
                    sh "docker push ${env.username}/chat-app-backend:v${env.BUILD_NUMBER}"
                }
            }
        }
    }
}
