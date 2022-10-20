pipeline {
    
    agent any
    
    environment {
        imageName = "test102"
        registryCredentials = "Nexus"
        registry = "35.175.120.92:8085/"
        dockerImage = ''
    }
    
    stages {
        stage('Git Code') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: '', url: 'https://github.com/kavita1309/test.git']]])                   }
        }
        stage('Sonarqube') {
               
             steps{
             withSonarQubeEnv(installationName: 'sonar', credentialsId: 'sonar') {
                 sh "/var/lib/jenkins/tools/sonar-scanner/bin/sonar-scanner"
                }
                
           }
        }
    
    // Building Docker images
    stage('Build an image') {
      steps{
        script {
          dockerImage = docker.build imageName
        }
      }
    }

    // Uploading Docker images into Nexus Registry
    //stage('Upload To Nexus') {
    //steps{  
      //  script {
          // docker.withRegistry( 'http://'+registry, registryCredentials ) {
            //dockerImage.push('latest')
          //}
        //}
      //}
   // } 

    // Uploading Docker images into Nexus Registry
    stage('Image upload to Nexus') {
     steps{
        script {
             docker.withRegistry( 'http://'+registry, registryCredentials ) {
              version = VersionNumber(versionNumberString: '1.${BUILDS_ALL_TIME}')
              version = VersionNumber(versionNumberString: '1.${BUILDS_ALL_TIME}')
             dockerImage.push(version)
           }
        }
      }
   }
    
    // Stopping Docker containers for cleaner Docker run
     
    stage('stop previous containers') {
         steps {
            sh 'docker ps -f name=test102 -q | xargs --no-run-if-empty docker container stop'
            sh 'docker container ls -a -fname=test102 -q | xargs -r docker container rm'
         }
       }
  
    stage('Docker Run') {
       steps{
         script {
               dockerImage.run("-p 80:80 --rm --name test102")
            }
         }
      }    
    }
}