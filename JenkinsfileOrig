//noinspection GroovyAssignabilityCheck
pipeline {
    agent any
  //  {
   //     label 'Linux'
   // }
    parameters {
        string(name: 'version', defaultValue: '', description: 'Version override appended to maven version number')
               } 
    //tools {
    //    jdk 'jdk8'
    //    maven 'maven'
    //}
    stages {
        stage('Build') {
            steps {
                // git branch: 'master', url: 'https://github.com/ovkhasch/petclinic-repo'
				sh 'yum install maven -y'
                sh 'mvn clean package -DskipTests=true'
            }
        }
        stage('Build docker images') {
                  environment {
                  nexus_docker_repo = 'docker-nexus.ingress.test.dcpgreendot.com'
                  dcp_demo_app_tag = 'petclinic'
                  PETCLINIC_VERSION = sh (
                      script: 'xf=`ls target/*.jar` && echo $xf | awk \'{print substr($1,1,match($1,/.[^.]*$/)-1)}\' | awk \'{print substr($1,match($1,/[0-9]/))}\'', 
                      returnStdout: true
                    ).trim()
                    PETCLINIC_VERSION_CUSTOM = "${PETCLINIC_VERSION}${params.version}"
      
                              }       
            
            steps {               
              withCredentials([usernamePassword(credentialsId: 'NexusID', usernameVariable: 'nexus_docker_repo_user', passwordVariable: 'nexus_docker_repo_password')]){

               sh '''#!/bin/bash -l

                 echo "Publish Docker Image to Nexus"
                
                 docker login -u ${nexus_docker_repo_user} -p ${nexus_docker_repo_password} ${nexus_docker_repo}

                 docker build -t ${nexus_docker_repo}/${dcp_demo_app_tag}:${PETCLINIC_VERSION_CUSTOM} -f Dockerfile .

                 docker push ${nexus_docker_repo}/${dcp_demo_app_tag}:${PETCLINIC_VERSION_CUSTOM}
                
                 '''
              }                
                //script {
                    
                 //   echo "Petclinic version: ${PETCLINIC_VERSION}"

                    //docker.withRegistry('https://registry.kaiburr.com', 'chef01-registry') {
                      //  def petclinicImage = docker.build("petclinic:${PETCLINIC_VERSION}", '.')
                       // petclinicImage.push()
                    //}
                //}
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'target/*.jar'
        }
    }
}
