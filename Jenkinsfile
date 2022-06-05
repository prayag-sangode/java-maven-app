pipeline {
  environment {
    imagename = "prayags/java-maven-app"
    registryCredential = 'dockerhub-id'
    dockerImage = ''
  }
  agent any
  stages {
    stage('Cloning Git') {
      steps {
        git([url: 'https://github.com/prayag-sangode/java-maven-app.git', branch: 'main', credentialsId: 'github-id'])

      }
    }
    stage('Build Docker Image') {
      steps{
        script {
          dockerImage = docker.build imagename
        }
      }
    }
    stage('Push Docker Image') {
      steps{
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push("$BUILD_NUMBER")
             dockerImage.push('latest')

          }
        }
      }
    }
    stage('Remove Unused docker image') {
      steps{
        sh "docker rmi $imagename:$BUILD_NUMBER"
        sh "docker rmi $imagename:latest"

      }
    }
    stage('Deploy Java Spring Boot App on microk8s') {
      steps{
       withKubeConfig([credentialsId: 'microk8s-id', serverUrl: 'https://10.160.0.2:16443']) {
        sh 'kubectl apply -f deploy.yaml'
     }
   }     
  }
 }
}
