pipeline {
    agent any
    options { disableConcurrentBuilds() }
    stages {
	
	stage('Permissions','clean up') {
	     agent {
		 docker {
		    image 'jenkinsci/jnlp-slave:latest'
		    label 'docker'
		    args '-v $HOME/.m2:/root/.m2'
		 }
	      }
             steps {
                  sh ''' chmod 775 *
		        ./gradlew --no-daemon clean 
	          '''
	    }
	}
                         
        stage('Check Style, FindBugs, PMD') {
		agent {
		  docker {
		      image 'jenkinsci/jnlp-slave:latest'
		      label 'docker'
		      args '-v $HOME/.m2:/root/.m2'
		  }
		}
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
	stage ('Deploy') {
		
		agent { dockerfile true }
		steps {
			script {
				 def img=docker.build('person:latest')
				 docker.image(img)
			}
		}
	}
			       
    }
 }
