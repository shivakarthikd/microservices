#!/usr/bin/env groovy
pipeline {
    agent any
    triggers {
        pollSCM('*/15 * * * *')
    }
    options { disableConcurrentBuilds() }
    stages {
	stage('Permissions') {
            steps {
		    script {
			echo 'Starting to build docker image'
			def myEnv = docker.image('gradle:latest') 
                
			    myEnv.inside {
			         sh 'chmod 775 *'
			    } 
		    }
            }
        }
		
	 stage('Cleanup') {
              steps {
		      script {
			    myEnv.inside {
                                    sh './gradlew --no-daemon clean'
			    }
		    }
            }
        }    
        
        stage('Check Style, FindBugs, PMD') {
            steps {
		    script{
			    myEnv.inside {
                         		sh './gradlew --no-daemon checkstyleMain checkstyleTest findbugsMain findbugsTest pmdMain pmdTest cpdCheck'
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
		  }
	   }				
      }
        stage('Test') {
            steps {
		    script {
	                 myEnv.inside {
		            sh './gradlew --no-daemon check'
			 }
		  }	
             }
            post {
                always {
                    junit 'build/test-results/test/*.xml'
                }
            }
        }
        stage('Build') {
            steps {
		    script {
		          myEnv.inside {
			      sh './gradlew --no-daemon build'
			  }
		    }
		 }
            }
    }
 }
