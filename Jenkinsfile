pipeline {
    agent {
        docker {
            image 'docker/compose:1.29.2'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }
    parameters {
        choice(name: 'VERSION', choices: ['1.1.0', '1.2.0', '1.3.0'], description: '')
        booleanParam(name: 'executeTests', defaultValue: true, description: '')
    }
    stages {
        stage('init') {
            steps {
                script {
                    gv = load "script.groovy"
                }
            }
        }
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t flask-jenkins:v1.0.0 -t flask-jenkins:latest .'
            }
        }
        stage('test') {
            when {
                expression {
                    params.executeTests
                }
            }
            steps {
                script {
                    gv.testApp()
                }
            }
        }
        stage('Tag and Push') {
            steps {
                withCredentials([[$class: 'UsernamePasswordMultiBinding',
                credentialsId: 'docker-hub', 
                usernameVariable: 'DOCKER_USER_ID', 
                passwordVariable: 'DOCKER_USER_PASSWORD'
                ]]) {
                    sh "docker tag flask-jenkins:latest ${DOCKER_USER_ID}/jenkins-app:${VERSION}"
                    sh "docker login -u ${DOCKER_USER_ID} -p ${DOCKER_USER_PASSWORD}"
                    sh "docker push ${DOCKER_USER_ID}/jenkins-app:${VERSION}"
                }
            }
        }
        stage('deploy') {
            steps {
                script {
                    // 현재 실행 중인 동일한 이름의 컨테이너를 중지하고 제거
                    sh "docker stop flask-jenkins || true"
                    sh "docker rm flask-jenkins || true"
                    // 새로운 컨테이너를 백그라운드에서 실행
                    sh "docker run -d --name flask-jenkins -p 5000:5000 ${DOCKER_USER_ID}/jenkins-app:${VERSION}"
                }
            }
        }
    }
}
