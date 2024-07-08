pipeline {
    agent any
    parameters {
        choice(name: 'VERSION', choices: ['1.1.0', '1.2.0', '1.3.0'], description: 'Select the version to deploy')
        booleanParam(name: 'executeTests', defaultValue: true, description: 'Execute tests before deploying')
    }
    stages {
        stage("Checkout") {
            steps {
                checkout scm
            }
        }
        stage("Build") {
            steps {
                sh 'docker-compose build web'
            }
        }
        stage("Tag and Push") {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub', 
                                                usernameVariable: 'DOCKER_USER_ID', 
                                                passwordVariable: 'DOCKER_USER_PASSWORD')]) {
                    sh "docker tag jenkins-pipeline_web:latest ${DOCKER_USER_ID}/jenkins-app:${VERSION}"
                    sh "echo ${DOCKER_USER_PASSWORD} | docker login -u ${DOCKER_USER_ID} --password-stdin"
                    sh "docker push ${DOCKER_USER_ID}/jenkins-app:${VERSION}"
                }
            }
        }
        stage("Deploy") {
            steps {
                sh "docker-compose up -d"
            }
        }
    }
}
