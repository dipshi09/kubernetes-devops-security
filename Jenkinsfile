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
stage('SonarQube - SAST') {
      steps {
        withSonarQubeEnv('sonarqube') {
          sh "mvn sonar:sonar \
              -Dsonar.projectKey=devsecops-numeric-application \
              -Dsonar.host.url=http://devsecops.eastasia.cloudapp.azure.com:9000"
      }
        timeout(time: 2, unit: 'MINUTES') {
          script {
            waitForQualityGate abortPipeline: true
          }
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
                   withCredentials([usernamePassword(credentialsId: 'dockerlogin', passwordVariable: 'dockerloginPassword', usernameVariable: 'dockerloginUser')]) {
                           sh "docker login -u ${env.dockerloginUser} -p ${env.dockerloginPassword}"
                           sh "docker push dipshi/java-app2:latest"
                  }	  
     
		
		  }
		  
		} 	
	  stage('kubernets Deploy') {
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']){
                sh 'kubectl apply -f k8s_deployment_service.yaml'
                }
            }
            
        }	
  }
}
