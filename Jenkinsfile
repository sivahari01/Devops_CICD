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
                git branch: 'main', url: 'https://github.com/Krishpluto/BoardgameListingWebApp.git'
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
                -Dsonar.host.url=http://54.210.95.42:9000/ \
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
                        sh 'docker login -u sivaharis -p Docker@100'
                        sh 'docker build -t shopping:latest .'
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
