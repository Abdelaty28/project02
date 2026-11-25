pipeline{
  agent{ label "Jenkins-Agent"}
  tools{
    jdk "Java17"
    maven "Maven3"
  }
  environment {
          DOCKER_USER = "11mahmoud04"
          DOCKER_CREDS = "docker-token"
          IMAGE_NAME = "${DOCKER_USER}/project02"
          IMAGE_TAG = "${BUILD_NUMBER}"
  }
  stages{
      stage("Cleanup Workspace"){
          steps{
              cleanWs()
          }
      }
      stage("Checkout from SCM"){
          steps{
              git branch: 'main', credentialsId: 'github', url: 'https://github.com/Abdelaty28/project02.git'
          }
      }
      stage("Build App"){
          steps{
              sh 'mvn clean package'
          }
      }
      stage("Test App"){
          steps {
              sh 'mvn test'
          }
      }
      stage("SonarQube Analysis") {
          steps {
              withSonarQubeEnv('sonarqube') {
              sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.0.0.4389:sonar'
              }
          }
      }
      stage("Quality Gate") {
          steps {
              timeout(time: 5, unit: 'MINUTES') {
              waitForQualityGate abortPipeline: false
              }
          }
      }
      stage("Build & Push Docker Image") {
            steps {
                withCredentials([usernamePassword(credentialsId: "${DOCKER_CREDS}",
                                                  usernameVariable: 'USER',
                                                  passwordVariable: 'PASS')]) {
                    sh '''
                        docker build -t $IMAGE_NAME:$IMAGE_TAG -t $IMAGE_NAME:latest .
                        echo "$PASS" | docker login -u "$USER" --password-stdin
                        docker push $IMAGE_NAME:$IMAGE_TAG
                        docker push $IMAGE_NAME:latest
                        docker logout
                    '''
                }
            }
      }
  }
}
