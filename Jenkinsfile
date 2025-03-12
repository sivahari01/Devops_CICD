pipeline {
    agent any

    environment {
        PROJECT_NAME = 'Shopping-app'
        BUILD_DIR = 'build'
        SCANNER_HOME = tool 'sonar-scanner'
    }

    tools {
        jdk 'JDK21'
        maven 'Maven3'
    }

    parameters {
        string(name: 'SONARQUBE', defaultValue: '', description: 'SonarQube Token from GitHub Secrets')
        string(name: 'DOCKERPASS', defaultValue: '', description: 'DockerHub Password from GitHub Secrets')
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo 'Checking out source code...'
                git branch: 'main', url: 'https://github.com/sivahari01/vprofile-project.git'
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
                -Dsonar.login=$SONARQUBE \
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
        
        stage('Build App') {
            steps {
                sh 'mvn clean test'
                sh 'mvn clean install'
                sh 'mvn clean package'
            }
        }
       
        stage('Build and Push Docker Image') {
            steps {
                script {
                    sh 'docker login -u sivaharis -p $DOCKERPASS'
                    sh 'docker build -t pet1:latest .'
                    sh 'docker tag pet1:latest sivaharis/pet1:latest'
                    sh 'docker push sivaharis/pet1:latest'
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline execution completed. Cleaning up...'
            // cleanWs()
        }

        success {
            echo 'Pipeline completed successfully!'
        }

        failure {
            echo 'Pipeline failed!'
        }
    }
}
