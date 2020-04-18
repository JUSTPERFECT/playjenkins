pipeline {

  environment {
    registry = "justperfect/k8s-jenkins"
    registryCredential = 'dockerhub'
    dockerImage = ""
    RELEASE_VERSION = 5
  }

  agent any

  stages {
    stage('Checkout Source') {
      steps {
        git 'https://github.com/justperfect/playjenkins.git'
      }
    }

    stage('Build image') {
      steps{
        script {
          dockerImage = docker.build registry + ":$RELEASE_VERSION"
        }
      }
    }

    stage('Push Image') {
      steps{
        script {
          docker.withRegistry( "", registryCredential ) {
            dockerImage.push()
          }
        }
      }
    }

    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $registry:$RELEASE_VERSION"
      }
    }
    stage('pull latest image') {
      steps{
        script {
          sh "sed -i 's/RELEASE_VERSION/$RELEASE_VERSION/g' myweb.yaml"
        }
      }
    }
    stage('Deploy App') {
      steps {
        script {
          kubernetesDeploy(configs: "myweb.yaml", kubeconfigId: "mykubeconfig")
        }
      }
    }
  }
}
