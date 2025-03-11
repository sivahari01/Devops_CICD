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
                git branch: 'main', url: 'https://github.com/jaiswaladi246/Ekart.git'
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
                -Dsonar.login=squ_5ecd22d74e6ea7a3ba26df8aec5e9b6dea4e472e \
                -Dsonar.projectName=Shopping-cart \
                -Dsonar.projectKey=shopping-cart \
                -Dsonar.java.binaries=.
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully!'
        }
        failure {
            echo 'Pipeline failed!'
        }
    }
}
