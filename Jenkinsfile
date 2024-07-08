pipeline {
   agent any
   environment {
      DOCKER_IMAGE = "skawjdgns12/flaskapp:v1.0.0"
      DOCKER_REGISTRY = "docker.io"
      DOCKER_USER_ID = credentials('docker-hub-username')
      DOCKER_USER_PASSWORD = credentials('docker-hub-password')
   }
   stages {
      stage("Checkout") {
         steps {
            checkout scm
         }
      }
      stage("Build") {
         steps {
            script {
               docker.build(DOCKER_IMAGE) 
            }
         }
      }
      stage("Test") {
         steps {
            echo "Testing the application..."
         }
      }
      stage("Tag and Push") {
         steps {
            script {
               docker.withRegistry("${DOCKER_REGISTRY}", "docker-credentials") {
                  def tag = "v1.0.0-${BUILD_NUMBER}"
                  docker.image(DOCKER_IMAGE).tag("${DOCKER_USER_ID}/jenkins-app:${tag}")
                  docker.image("${DOCKER_USER_ID}/jenkins-app:${tag}").push()
               }
            }
         }
      }
      stage("Deploy") {
         steps {
            echo "Deploying the application..."
         }
      }
   }
}
