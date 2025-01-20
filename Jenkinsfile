pipeline {
  agent any
    // JAVA와 Maven Tool 등록
  tools {
    jdk 'JDK17'
    maven 'M3'
  }

  // Docker Hub 등록
  environment {
    DOCKERHUB_CREDENTIALS = credentials('dockerCredential')
    AWS_CREDENTIALS = credentials('AWSCredentials')
    //GIT_CREDENTIALS = credentials('gitCredential')
    REGION = 'ap-northeast-2'
  }
  
  stages {
    // GitHub에 가서 소스코드가져오기
    stage('Git Clone') {
      steps {
        echo 'Gig Clone'
        git url: 'https://github.com/mkw70/spring-petclinic.git',
          branch: 'main'
      }
    }
    // Maven 빌드 작업
    stage('Maven Build') {
      steps {
        echo 'Maven Build'
        sh 'mvn -Dmaven.test.failure.ignore=true clean package'
      }
    }
    // DockerImage생성
    stage('Docker Image Build') {
      steps {
        echo 'Docker Image build'
        dir("${env.WORKSPACE}") {
          sh """
          docker build -t m7098/spring-petclinic:$BUILD_NUMBER .
          docker tag m7098/spring-petclinic:$BUILD_NUMBER m7098/spring-petclinic:latest
          """
        }
      }
    }

    // DockerHub Login
    stage('Docker Login') {
      steps {
        sh """
        echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin
        docker push m7098/spring-petclinic:latest
        """
      }
    }
    
    // Docker Image 삭제
    stage('Remove Docker Image') {
      steps {
        sh """
        docker rmi m7098/spring-petclinic:$BUILD_NUMBER
        docker rmi m7098/spring-petclinic:latest
        """
      }
    }

    // s3에 Appspec.yml Upload
    stage('Upload to s3') {
      steps {
        echo "Upload to s3"
        dir("${env.WORKSPACE}") {
          sh 'zip -r deploy.zip ./deploy appspec.yml'
          withAWS(region:"${REGION}", credentials:"${AWS_CREDENTIALS_NAME}"){
            s3Upload(file:"deploy.zip", bucket:"user17-codedeploy-bucket")
          }
          sh 'rm -rf ./deploy.zip'
        }
      }
    }


    
       
  }
}

