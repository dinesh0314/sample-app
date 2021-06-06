pipeline {
    agent {
        docker {
            image 'maven:3-alpine' 
            args '-v /root/.m2:/root/.m2' 
        }
    }  
 stages {
      stage('Build') { 
            steps {
                sh 'mvn -B -DskipTests clean package' 
            }
        }
      stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit '*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
      stage('checkout') {
           steps {
             
                git branch: 'main', url: 'https://github.com/dinesh0314/app-tomcat-java.git'
             
          }
        }
  stage('Docker Build and Tag') {
           steps {
              
                sh 'docker build -t samplewebapp:latest .' 
                sh 'docker tag samplewebapp dinesh0314/samplewebapp:latest'
                //sh 'docker tag samplewebapp dinesh0314/samplewebapp:$BUILD_NUMBER'
               
          }
        }
     
  stage('Publish image to Docker Hub') {
          
            steps {
       withCredentials([usernamePassword(credentialsId: 'dockerhub', passwordVariable: 'dockerHubPassword', usernameVariable: 'dockerHubUser')]) {
          sh "docker login -u ${env.dockerHubUser} -p ${env.dockerHubPassword}"
          sh  'docker push dinesh0314/samplewebapp:latest'
        //  sh  'docker push dinesh0314/samplewebapp:$BUILD_NUMBER' 
        }
                  
          }
        }
     
      stage('Run Docker container on Jenkins Agent') {
             
            steps 
			{
                sh "docker run -d -p 8003:8080 dinesh0314/samplewebapp"
 
            }
        }
 stage('Run Docker container on remote hosts') {
             
            steps {
                sh "docker -H ssh://jenkins@192.168.56.23 run -d -p 8003:8080 dinesh0314/samplewebapp"
 
            }
        }
    }
	}
    
