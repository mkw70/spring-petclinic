pipeline {
  agent any
    // JAVA와 Maven Tool 등록
  tools {
    jdk 'JDK17'
    maven 'M3'
  }

  // Docker Hub 등록
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerCredentials')
    AWS_CREDENTIALS = credentials('AWSCredentials')
    GIT_CREDENTIALS = credentials('gitCredentials')
    REGION = 'ap-northeast-2'
  }
  
  stages {
    // GitHub에 가서 소스코드가져오기
    stage('Git Clone') {
      steps {
        echo 'Gig Clone'
        git url: 'https://github.com/mkw70/spring-petclinic.git',
          branch: 'main', credentialIdL: 'GIT_CREDNTIALS'
      }
    }
  }
}

