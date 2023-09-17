pipeline {
  agent any
  tools { 
        maven 'maven_3_5_2'  
    }
   stages{
    stage('CompileandRunSonarAnalysis') {
            steps {	
		  withCredentials([string(credentialsId: 'SONAR_TOKEN', variable: 'TOKEN')]){
		     sh 'mvn clean verify sonar:sonar -Dsonar.projectKey=anuja2015devsecopsbuggyapp -Dsonar.organization=anuja2015devsecops -Dsonar.host.url=https://sonarcloud.io -Dsonar.login=${TOKEN}'
		  }
	    }
        } 
     stage('SCAAnalysisUsingSynk') {
             steps {
		   withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]){
		     sh 'mvn snyk:test -fn'
		   }
	     }
     }

      stage('Build') { 
              steps { 
               withDockerRegistry([credentialsId: "DockerLogin", url: ""]) {
                 script{
                 app =  docker.build("easybuggyapp")
                 }
               }
            }
      }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://196407767051.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:AWS_CREDENTIALS') {
                    app.push("latest")
                    }
                }
            }
    	}
        stage('Kubernetes Deployment of EasyBuggy Web Application') {
	   steps {
	      withKubeConfig([credentialsId: 'KubeConfig']) {
		  sh('kubectl delete all --all -n devsecops')
		  sh ('kubectl apply -f deployment.yaml --namespace=devsecops')
		}
	      }
   	}

	    
       
	    
  }
}
