podTemplate(yaml: '''
apiVersion: v1
kind: Pod
spec:
  containers:
  - name: docker
    image: docker:20.10.17-dind
    securityContext:
      privileged: true
    env:
      - name: DOCKER_TLS_CERTDIR
        value: ""
''') 

{
    node(POD_LABEL) {
        container('docker') { 
       
    //   def registry = "hend91/node-board" 
        stage('Checkout from GitHub') {
                checkout([
                    $class: 'GitSCM',
                    branches: [[name: "*/master"]],
                    extensions: [],
                    userRemoteConfigs: [[
                        url: 'https://github.com/hendRMS/bulletin-board-app-devops.git'
                        ]]
                ])
            }

        stage('Update the code') {
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
        stage('Build Docker Image') {
                script {
                    echo "====Building Docker Image===="
                    dockerImage = docker.build "hend91/node-board" + ":$BUILD_NUMBER"       
                        }  
            }
        //
        stage('Push Docker Image') {
                script {
                  echo "====Push Docker Image===="
                   withCredentials([usernamePassword(credentialsId: 'DockerHubCredentials', passwordVariable: 'DOCKER_REGISTRY_PWD', usernameVariable: 'DOCKER_REGISTRY_USER')]) {
                   sh 'docker login -u ${DOCKER_REGISTRY_USER} -p ${DOCKER_REGISTRY_PWD}'
                    dockerImage.push() 
        }

                        }    
                   }
        }
    }
}


