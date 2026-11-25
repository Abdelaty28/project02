pipeline{
  agent{ label "Jenkins-Agent"}
  tools{
    jdk "Java17"
    maven "Maven3"
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
  }
}
