pipeline {
  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
    disableConcurrentBuilds()
  }
  agent any
  environment {
    IMAGE_NAME      = "rpmbuild"
    TEMP_IMAGE_NAME = "rpmbuild7_${BUILD_NUMBER}"
    TAG_NAME        = "el7"
  }
  stages {
    stage('Prepare') {
      steps {
        sh 'docker pull centos:7'
      }
    }
    stage('Build') {
      steps {
        sh 'FINAL_IMAGE_NAME="${DOCKER_PRIVATE_REGISTRY}/${IMAGE_NAME}:${TAG_NAME}"'
        sh 'docker build --no-cache --squash --compress -t ${TEMP_IMAGE_NAME} .'
      }
    }
    stage('Publish') {
      steps {
        sh 'docker tag ${TEMP_IMAGE_NAME} ${DOCKER_PRIVATE_REGISTRY}/${IMAGE_NAME}:${TAG_NAME}'
        sh 'docker push ${DOCKER_PRIVATE_REGISTRY}/${IMAGE_NAME}:${TAG_NAME}'
      }
    }
  }
  triggers {
    bitbucketPush()
  }
  post {
    success {
      juxtapose event: 'success'
      sh 'figlet "SUCCESS"'
    }
    failure {
      juxtapose event: 'failure'
      sh 'figlet "FAILURE"'
    }
    always {
      sh 'docker rmi  $TEMP_IMAGE_NAME'
    }
  }
}
