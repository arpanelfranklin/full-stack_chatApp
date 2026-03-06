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
        stage("trivy"){
            steps{
                sh "trivy fs . -o result.json"
            }
        }
        stage("Docker-Push"){
            steps{
                withCredentials([usernamePassword(
                    credentialsId: "dockerHubCreds",
                    usernameVariable: "dockerHubUser",
                    passwordVariable: "dockerHubPass"
                    )]){
                    sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPass}"
                    sh "docker image tag chat-app-frontend:v${env.BUILD_NUMBER} ${env.dockerHubUser}/chat-app-frontend:v${env.BUILD_NUMBER}"
                    sh "docker image tag chat-app-backend:v${env.BUILD_NUMBER} ${env.dockerHubUser}/chat-app-backend:v${env.BUILD_NUMBER}"
                    sh "docker push ${env.dockerHubUser}/chat-app-frontend:v${env.BUILD_NUMBER}"
                    sh "docker push ${env.dockerHubUser}/chat-app-backend:v${env.BUILD_NUMBER}"
                    sh "sed -i 's|image: ${env.dockerHubUser}/chat-app-backend.*|image: ${env.dockerHubUser}/chat-app-backend:v${env.BUILD_NUMBER}|g' ./k8s/backend-deployment.yml"
                    sh "sed -i 's|image: ${env.dockerHubUser}/chat-app-frontend.*|image: ${env.dockerHubUser}/chat-app-frontend:v${env.BUILD_NUMBER}|g' ./k8s/fronted-deployment.yml"
                }
            }
        }
        stage("deploy"){
            steps{
                sh "kubectl apply -f ./k8s/namespace.yml"
                sh "kubectl apply -f ./k8s/.
                
                }
            }
    }
    post{
        success{
            script{
                emailext from: "arpanel07@gmail.com",
                subject: "Build Sucess",
                body: "BUild succesfully yayyy! you can access app in port 9090",
                attachmentsPattern: '**/result.json',
                to: "stfu.arpanel@gmail.com"
            }
        }
        failure{
             script{
                emailext from: "arpanel07@gmail.com",
                subject: "Build failed",
                body: "BUild failed oops! check logs and try again",
                attachmentsPattern: '**/result.json',
                to: "stfu.arpanel@gmail.com"
        }
    }
}
}
