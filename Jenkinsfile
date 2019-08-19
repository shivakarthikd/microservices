pipeline {
    environment {
       registry = "shivakarthik/microservice"
       registryCredential = 'dockerhub'
       dockerImage = ''
   }
    agent { label 'docker' }
    options { disableConcurrentBuilds() }
    stages {
	
	stage('Permissions,clean up') {
             steps {
                  sh ''' chmod 775 *
		        ./gradlew --no-daemon clean 
	          '''
	    }
	}
                         
        stage('Check Style, FindBugs, PMD') {
		steps {
                        sh './gradlew --no-daemon checkstyleMain checkstyleTest findbugsMain findbugsTest pmdMain pmdTest cpdCheck'
			    
	     }
             post {
                     always {
				step([
					$class         : 'FindBugsPublisher',
					pattern        : 'build/reports/findbugs/*.xml',
					canRunOnFailed : true
				])
				step([
					$class         : 'PmdPublisher',
					pattern        : 'build/reports/pmd/*.xml',
					canRunOnFailed : true
				])
				step([
					$class         : 'CheckStylePublisher', 
					pattern        : 'build/reports/checkstyle/*.xml',
					canRunOnFailed : true
				])
			}
		}		
      }
        stage('Test') {
		steps {
		      sh './gradlew --no-daemon check'
             }
            post {
                always {
                    junit 'build/test-results/test/*.xml'
                }
            }
        }
        stage('Build') {
		steps {
	             sh './gradlew --no-daemon build'
			
		 }
            }
         stage('copy artifacts'){
		    
		 steps {
			 copyArtifacts filter: '/home/jenkins/workspace/Microservices/build/libs/*.jar', fingerprintArtifacts: true, projectName: 'Microservices', selector: lastSuccessful(), target: '/jenkins/targets'
		 }
	 }
	    
	 stage('Update Docker UAT image') {
              when { branch "master" }
              steps {
		      script {
		   
                             docker.build registry + ":$BUILD_NUMBER"
		   }
            }
	 }
	 stage('Deploy Image') {
		when { branch "master" }
                steps{
                    script {
                        docker.withRegistry( '', registryCredential ) {
                        dockerImage.push()
			}
                    }
                }
         }
     
			       
    }
 }
