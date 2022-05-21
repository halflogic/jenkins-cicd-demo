pipeline {
    agent any
    
    environment {
        GIT_REPO = 'https://github.com/halflogic/cicd-pipeline-train-schedule-kubernetes.git'
        GIT_CREDS = 'github-key'
        GIT_BRANCH = '*/master'
        DOCKER_IMAGE_NAME = 'halflogic/train-schedule'
        DOCKER_REGISTRY = 'https://registry.hub.docker.com'
        DOCKER_CREDS = 'dockerhub-login'
        ARTIFACT_FILE = 'dist/trainSchedule.zip'

    }

    stages {
        stage('Checkout') {
            steps {
                checkout([
                    $class: 'GitSCM', branches: [[name: "${env.GIT_BRANCH}"]], 
                    doGenerateSubmoduleConfigurations: false, 
                    extensions: [], 
                    submoduleCfg: [], 
                    userRemoteConfigs: [[
                        credentialsId: "${env.GIT_CREDS}", 
                        url: "${env.GIT_REPO}"]]
                        ])
            }
        }
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: "${env.ARTIFACT_FILE}"
            }
        }
        stage('Push Docker Image') {
            steps {
                script {
                    app = docker.build("${env.DOCKER_IMAGE_NAME}")
                    docker.withRegistry("${env.DOCKER_REGISTRY}", "${env.DOCKER_CREDS}") {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
    }
}
