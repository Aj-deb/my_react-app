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
    stage("Install docker"){
            steps{
                sh '''
                # Add Docker's official GPG key:
                sudo apt update
                sudo apt install ca-certificates curl
                sudo install -m 0755 -d /etc/apt/keyrings
                sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
                sudo chmod a+r /etc/apt/keyrings/docker.asc

                # Add the repository to Apt sources:
                sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
                Types: deb
                URIs: https://download.docker.com/linux/ubuntu
                Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
                Components: stable
                Architectures: $(dpkg --print-architecture)
                Signed-By: /etc/apt/keyrings/docker.asc
                EOF

                sudo apt update
                sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
                sudo usermod -aG docker $USER
                sudo newgrp docker
                '''
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