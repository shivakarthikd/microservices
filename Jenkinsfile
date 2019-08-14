pipeline {
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
			       
    }
 }
