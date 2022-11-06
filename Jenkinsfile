pipeline {

        agent any
    
    
    	environment {
        registry="ademdaami/devopsproject"
        registryCredential='DockerHub'
        dockerImage =''
    }
    
        stages {
                stage('Check out Git'){
                   
                steps{
                        echo 'Pulling...';
                        git branch: 'Adem',
                        url : 'https://github.com/daamiadem/ProjetDevops.git';
                    }
                }
                
                
          
       
        stage('Testing maven') {
            steps {
                sh """mvn -version"""
                 
            }
        }
       
        stage('CLEAN Install') {
            steps {
                sh 'mvn clean'
                sh 'mvn install'
                 
            }
        }
        stage('MVN COMPILE') {
            steps {
                sh 'mvn compile'
                 
            }
        }
        
		stage('SonarQube analysis 1') {
            steps {
                sh 'mvn sonar:sonar -Dsonar.login=admin -Dsonar.password=ademdaami'
            }
        }
        
        stage('Nexus Repository Manager') {
            steps {
                script {
					nexusArtifactUploader artifacts: [[artifactId: 'DevopsProject', classifier: '', file: 'target/DevopsProject-1.0.jar', type: 'jar']], credentialsId: 'NEXUS_CRED', groupId: 'com.esprit.examen', nexusUrl: '192.168.1.122:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-snapshots', version: '1.0.0-SNAPSHOT'
				}
            }}
       
        
        stage('JUnit and Mockito Test'){
            steps{
                script
                {
                    if (isUnix())
                    {
                        sh 'mvn --batch-mode test'
                    }
                    else
                    {
                        bat 'mvn --batch-mode test'
                    }
                }
            }
        
    }
    
    
     stage('Building image docker-compose') {
          steps {

              sh "docker-compose up"
          }
        }
        
     
     
     stage('Building image') {
      steps{
          dir("DevopsProject") {
        script {
            DOCKER_BUILDKIT=0
          dockerImage = docker.build registry + ":$BUILD_NUMBER"
        }
      }
      }
    }
    stage('Push Docker Image') {
      steps{
             dir("DevopsProject") {
        script {
          docker.withRegistry( '', registryCredential ) {
            dockerImage.push()
          }}
        }
      }
    }
   stage('Remove Unused docker image') {
      steps{
          
          bat "docker rmi $registry:$BUILD_NUMBER"
      }
    } 


     
    	
    	
    	
    	
    	
}}