pipeline {
    agent any

    triggers {
        pollSCM('H/2 * * * *')
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    environment {
        // Replace YOUR_DOCKERHUB_USERNAME with your actual Docker Hub username.
        DOCKERHUB_REPO = 'YOUR_DOCKERHUB_USERNAME/devops-pipeline-app'
        CONTAINER_NAME = 'devops-pipeline-app'
        APP_PORT = '3000'
    }

    stages {
        stage('1. Checkout') {
            steps {
                checkout scm
            }
        }

        stage('2. Install') {
            steps {
                bat 'npm ci'
            }
        }

        stage('3. Test') {
            steps {
                bat 'npm test'
            }
        }

        stage('4. Docker Build') {
            steps {
                bat 'docker build -t "%DOCKERHUB_REPO%:%BUILD_NUMBER%" -t "%DOCKERHUB_REPO%:latest" .'
            }
        }

        stage('5. Docker Hub Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKERHUB_USER', passwordVariable: 'DOCKERHUB_TOKEN')]) {
                    bat '''@echo off
                    echo %DOCKERHUB_TOKEN%| docker login --username %DOCKERHUB_USER% --password-stdin
                    if errorlevel 1 exit /b 1
                    docker push "%DOCKERHUB_REPO%:%BUILD_NUMBER%"
                    if errorlevel 1 exit /b 1
                    docker push "%DOCKERHUB_REPO%:latest"
                    if errorlevel 1 exit /b 1
                    '''
                }
            }
        }

        stage('6. Deploy') {
            steps {
                bat '''@echo off
                docker rm -f "%CONTAINER_NAME%" 2>NUL
                docker run -d --name "%CONTAINER_NAME%" --restart unless-stopped -p %APP_PORT%:3000 "%DOCKERHUB_REPO%:%BUILD_NUMBER%"
                if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('7. Smoke Test') {
            steps {
                bat '''@echo off
                curl.exe --fail --silent --show-error --retry 10 --retry-delay 3 --retry-connrefused http://localhost:%APP_PORT%/health
                if errorlevel 1 exit /b 1
                '''
            }
        }
    }

    post {
        success {
            echo 'All seven stages completed successfully.'
        }
        failure {
            echo 'Pipeline failed. Review the stage logs above; later stages will not run after a failed stage.'
        }
    }
}
