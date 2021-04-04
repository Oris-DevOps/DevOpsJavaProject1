pipeline {
    agent any
  tools {
  maven 'Maven3'
  }
    stages {
        stage('Code Checkout') {
            steps {
               checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: '154a5161-a66a-4b60-8ba3-b2ec27e998ca', url: 'https://github.com/Oris-DevOps/DevOpsJavaProject1']]]) 
            }
        }
        
       
        stage ('Build') {
          steps {
          sh 'mvn clean install -f MyWebApp/pom.xml'
          }
        }   
        
        
        stage ('Code Quality') {
          steps {
            withSonarQubeEnv('SonarQube') {
            sh 'mvn -f MyWebApp/pom.xml sonar:sonar'
            }
          }
        }
        
        stage('Jacoco') {
            steps {
                jacoco()
            }
        }
        
        stage('Nexus Upload') {
            steps {
               nexusArtifactUploader artifacts: [[artifactId: 'MyWebApp', classifier: '', file: 'MyWebApp/target/MyWebApp.war', type: 'war']], credentialsId: '6db3b511-7aff-4870-8fb9-cba371c1c9a1', groupId: 'com.dept.app', nexusUrl: 'http://3.143.192.225:8081/', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-releases', version: '1.1' 
            }
        }
        
        
        stage ('Development Deployment') {
            steps {
                echo "deploying to DEV Env "
                deploy adapters: [tomcat9(credentialsId: '677902cd-0959-49dd-9d69-49e08690c910', path: '', url: 'http://18.217.16.141:8080/')], contextPath: null, war: '**/*.war'
            }
        }
        
        stage ('Slack Notification') {
            steps {
                echo "Generating Slack Notification"
                slackSend channel: 'jenkinschannel,general', message: "Job is successful sent to Jenkins & General channel, here is the info - Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"
            }
        }
        
        stage ('DEV Approve') {
            steps {
            echo "Taking approval from DEV Manager for QA Deployment"
            timeout(time: 7, unit: 'DAYS') {
            input message: 'Do you want to deploy?', submitter: 'admin'
            }   
            }
        }
        
    }
}
