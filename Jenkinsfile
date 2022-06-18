
pipeline {
    agent any
    environment { 
        registry = "hend91/node-board" 
        //registryCredential = 'dockerhub_id' 
        dockerImage = '' 
    }
    options {
        ansiColor('xterm')
        skipDefaultCheckout(true)
    }
    parameters {
        booleanParam(name: 'Refresh', defaultValue: false, description: "Refresh only pipeline configuration")
        string(name: 'branch', defaultValue: 'main', description: "Branch to build (will also be the Jenkinsfile source)")
    }
    stages {
        stage('Read Jenkins file') {
            when {
                expression { return params.Refresh == true }
            }
            steps {
                echo "stop"
            }
        }
        stage('Checkout from GitHub') {
            when {
                beforeAgent true
                expression { return params.Refresh == false }
            }
            steps {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/${params.branch}"]],
                    extensions: [],
                    userRemoteConfigs: [[
                     //   credentialsId: 'rflx-jenkins',
                        url: 'https://github.com/hendRMS/bulletin-board-app-devops.git'
                        ]]
                ])
                //withCredentials([file(credentialsId: "terraform-sa", variable: 'GC_KEY')]) {
                //    sh 'cp $GC_KEY creds.json'
               // }
            }
        }
        stage('Update the code') {
            when {
                beforeAgent true
                allOf {
                    expression { return params.Refresh == false };
                //    expression { return params.COMPONENT.contains("base_project") == true }

                }
            }
        }
        stage('Run startup script') {
            steps {
                script {
                        echo "====Running StartUp script ===="
                        sh """
                           sed -i 's/145/0,.1/g' site.css
                           sed -i 's/98/0/g' site.css
                           sed -i 's/41/0/g' site.css
                           sed -i 's/rgb/rgba/g' site.css
                           sed -i 's/380a66/366ec3/g' site.css
                           sed -i 's/Board/Board Demo!/g' index.html
                           """
                           }
                        }       
            }  
                        //}    
        stage('Build Docker Image') {
            steps {
                script {
                    echo "====Building Docker Image===="
                    dockerImage = docker.build registry + ":$BUILD_NUMBER"
                            
                        }    
                    }
                }
        stage('Push Docker Image') {
            steps {
                script {
                   echo "====Push Docker Image===="
                    dockerImage.push() 
                        }    
                    }
                }
        stage('Cleaning up') { 
           steps { 
              sh "docker rmi $registry:$BUILD_NUMBER" 
                   }
                } 
    }
}
