pipeline {
  agent any
  stages {
    stage('Build Artifact - Maven') {
      steps {
        sh "mvn clean package -DskipTests=true"
        archive 'target/*.jar'
      }
    }
    stage('Unit Tests - JUnit and Jacoco') {
      steps {
        sh "mvn test"
      }
      post {
        always {
          junit 'target/surefire-reports/*.xml'
          jacoco execPattern: 'target/jacoco.exec'
        }
      }
    }
	stage('Docker Build') {
		steps {
			sh "docker build -t dipshi/java-app2:latest ."
		  }
		  
		} 
	  stage('Docker Push') {
		steps {
                   withCredentials([usernamePassword(credentialsId: 'dockerlogin', usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
                           echo "username is $USERNAME"
			   sh "docker login"
			   sh "docker push dipshi/java-app2:latest"
                  }	  
     
			
		  }
		  
		} 		
  }
}
