def COLOR_MAP = [
    'SUCCESS': 'good', 
    'FAILURE': 'danger',
]


pipeline {
    agent any
    environment{
        MESSAGE= "hello everyone"
        WORKSPACE= "${env.WORKSPACE}"
    }
    tools{
      maven 'localMaven'
      jdk 'localJdk'
    }
    stages {
        stage('Git checkout') {
            steps {
                echo 'cloning the application code'
                git branch: 'main', url: 'https://github.com/asongsixtus/Jenkins-CI-CD-Pipeline-Project-.git'
               sh 'mvn --version'
    }
            }
        }
    }
        stage('Build') {
            steps {
              sh 'mvn clean package'
            }
             stage('Unit Test'){
        steps {
            sh 'mvn test'
        }
    }
    stage('Integration Test'){
        steps {
          sh 'mvn verify -DskipUnitTests'
        }
    }
    stage ('Checkstyle Code Analysis'){
        steps {
            sh 'mvn checkstyle:checkstyle'
        }
        post {
            success {
                echo 'Generated Analysis Result'
            }
        }
    }
    stage ('SonarQube scanning'){
        steps {
            withSonarQubeEnv('SonarQube') {
            sh """
                mvn sonar:sonar \
      -Dsonar.projectKey=JavaWebApp \
      -Dsonar.host.url=http://172.31.28.181:9000 \
      -Dsonar.login=72f6f88a9e4ef3f7d1940ac47bc77be33745a1fd
            """
        }
        }
    }
    stage("Quality Gate"){
  steps{
   waitForQualityGate abortPipeline: true
     }
    }
    stage("Upload artifacts to Nexus"){
  steps{
   sh 'mvn clean deploy'
     }
    }
    
    stage('Deploy to DEV') {
      environment {
        HOSTS = "dev"
      }
      steps {
        sh "ansible-playbook ${WORKSPACE}/deploy.yaml --extra-vars \"hosts=$HOSTS workspace_path=$WORKSPACE\""
      }
     }
     
     
         
    stage('Deploy to STAGE env') {
      environment {
        HOSTS = "stage"
      }
      steps {
        sh "ansible-playbook ${WORKSPACE}/deploy.yaml --extra-vars \"hosts=$HOSTS workspace_path=$WORKSPACE\""
      }
     }
     
     
     stage('Approval') {
      steps {
        input('Do you want to proceed?')
      }
    }

     
              
    stage('Deploy to PROD env') {
      environment {
        HOSTS = "prod"
      }
      steps {
        sh "ansible-playbook ${WORKSPACE}/deploy.yaml --extra-vars \"hosts=$HOSTS workspace_path=$WORKSPACE\""
      }
     }
     
     post { 
        always { 
            echo 'I will always say Hello again!'
            slackSend channel: '#glorious-w-f-devops-alerts', color: COLOR_MAP[currentBuild.currentResult], message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER} \n More info at: ${env.BUILD_URL}"
        }
    }

     
     
        post{
            sucess{
                echo 'archiving...'
            }
        }
         stage('Unit Test'){
        steps {
            sh 'mvn test'
        }
    }
    }