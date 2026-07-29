pipeline{
    agent{
            node{
                label 'build-agent-1'
            }
        }
    stages{
        stage('Checkout'){
            steps{
                checkout scm
            }
        }
        stage('Build Image'){
            steps{
                sh 'docker build -t my_app .' 
            }
        }
        stage('Pushing To HUB'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId:'dockerhubid',
                    usernameVariable:'DOCKER_USERNAME',
                    passwordVariable:'DOCKER_PASSWORD'
                )]){
                    sh """
                      echo "$DOCKER_PASSWORD" | docker login -u $DOCKER_USERNAME --password-stdin
                      docker tag my_app mahoragaadating/my_react_app:latest
                      docker push mahoragaadating/my_react_app:latest
                    """
                }
            }
        }
        stage('Deploy'){
            steps{
                sh '''
                    ssh -o StrictHostKeyChecking=no ubuntu@172.31.9.253 
                    docker pull mahoragaadating/my_react_app:latest
                    docker rm my_app
                    docker run  --name my_app mahoragaadating/my_react_app -p 5173:5173
                '''
            }
        }

    }
}