pipeline {
    agent any  // Run on any available agent

    environment {
        PROJECT_NAME = 'Shopping-app'
        BUILD_DIR = 'build'
        SCANNER_HOME = tool 'sonar-scanner'
    }

    tools {
        jdk 'JDK21'
        maven 'Maven3'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out source code...'
                git branch: 'main', url: 'https://github.com/uniquesreedhar/JavaApp-CICD.git'
            }
        }

        stage('Compile Code') {
            steps {
                echo 'Building the application...'
                sh 'mvn clean compile'
            }
        }
           
        stage('SonarQube Analysis') {
            steps {
                echo 'Running SonarQube analysis...'
                sh '''
                $SCANNER_HOME/bin/sonar-scanner \
                -Dsonar.host.url=http://3.89.97.137:9000/ \
                -Dsonar.login=squ_b43b98e2801fa8008ad00f5c948e4662e939cd25 \
                -Dsonar.projectName=Shopping-cart \
                -Dsonar.projectKey=shopping-cart \
                -Dsonar.java.binaries=.
                '''
            }
        }
        stage('OWASP Dependency Check') {
            steps {
                dependencyCheck additionalArguments: '--scan ./', odcInstallation: 'DP'
                  dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('Buid app'){
            steps{
                sh  'mvn clean test'
                sh 'mvn clean install'
                sh 'mvn clean package'
            }
        }
       
        stage('Build and Push Docker Image') {
            steps {
                script {
                    sh '''
                      

echo "Updating system packages..."
sudo apt update -y
sudo apt upgrade -y

echo "Installing required dependencies..."
sudo apt install -y apt-transport-https ca-certificates curl software-properties-common

echo "Adding Docker's official GPG key..."
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

echo "Adding Docker repository..."
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

echo "Updating package index..."
sudo apt update -y

echo "Installing Docker Engine..."
sudo apt install -y docker-ce docker-ce-cli containerd.io

echo "Starting Docker service..."
sudo systemctl enable docker
sudo systemctl start docker

echo "Adding current user to Docker group..."
sudo usermod -aG docker $USER

echo "Installing Docker Compose..."
DOCKER_COMPOSE_VERSION=$(curl -s https://api.github.com/repos/docker/compose/releases/latest | grep -oP '"tag_name": "\K(.*)(?=")')
sudo curl -L "https://github.com/docker/compose/releases/download/${DOCKER_COMPOSE_VERSION}/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

echo "Verifying Docker installation..."
docker --version
docker-compose --version

echo "Docker and Docker Compose installation completed successfully!"
echo "Please log out and log back in for group changes to take effect."
'''
                        sh 'docker login -u sivaharis -p Docker@100'
                        sh 'docker build -t shopping:latest -f docker/Dockerfile .'
                        sh 'docker tag shopping:latest sivaharis/shopping:latest'
                        sh 'docker push sivaharis/shopping:latest'
                }
            }
        }
    }
    post {
         always {
            echo 'Pipeline execution completed. Cleaning up...'
            cleanWs()
        }

        success {
            echo 'Pipeline completed successfully!'
        
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
