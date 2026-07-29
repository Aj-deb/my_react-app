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
        stage('DEPOLYING TO HUB'){
            steps{
                withCredentials([usernamePassword(
                    credentialsId:'dockerhubid',
                    usernameVariable:'DOCKER_USERNAME',
                    passwordVariable:'DOCKER_PASSWORD'
                )]){
                    sh """
                      echo "$DOCKER_PASSWORD" | docker login -u $DOCKER_USERNAME $DOCKER_PASSWORD --password-stdin
                      docker tag my_app mahoragaadating/my_react_app
                      docker push mahoragaadating/my_react_app
                    """
                }
            }
        }
        stage('Deploy'){
            steps{
                sh '''
                    sudo apt update
                    sudo apt install -y docker.io
                    sudo systemctl enable docker
                    sudo systemctl start docker
                    ssh -o StrictHostKeyChecking=no ubuntu@172.31.9.253 '
                    docker pull mahoragaadating/my_react_app:latest
                '''
            }
        }
        stage('Run container'){
            steps{
                sh "docker run --name my_app mahoragaadating/my_react_app -p 5173:5173"
            }
        }
    }
}