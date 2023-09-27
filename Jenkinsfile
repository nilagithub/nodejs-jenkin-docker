pipeline {
    agent any
    tools { nodejs "node"}
	options {
		buildDiscarder(logRotator(numToKeepStr: '2'))
	}

    environment {
        imageName = "nexrpa/node-app"
        registryCredential = 'nexrpa'
        dockerImage = ''
		image_tag = 'latest'
    }
    stages {
        stage("Install Dependencies"){
            steps{
                sh 'npm install'
            }
        }

        /*stage("Tests"){
            steps {
                sh 'npm test'
            }
        }*/
		stage("Cleaning Image"){
            steps {
                sh "docker image prune --filter=dangling=true --force"				
            }
        }
		

        stage("Building Image"){
            steps {
                //sh "docker build --no-cache -t ${imageName}:${image_tag} ."
				script {
                    dockerImage = docker.build imageName
                }
            }
        }
		stage('Get Portainer JWT Token') {
			steps {
				script {
				  withCredentials([usernamePassword(credentialsId: 'Portainer', usernameVariable: 'PORTAINER_USERNAME', passwordVariable: 'PORTAINER_PASSWORD')]) {
					  def json = """
						  {"Username": "$PORTAINER_USERNAME", "Password": "$PORTAINER_PASSWORD"}
					  """
					  def jwtResponse = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', validResponseCodes: '200', httpMode: 'POST', ignoreSslErrors: true, consoleLogResponseBody: true, requestBody: json, url: "https://62.72.57.97:9443/api/auth"
					  def jwtObject = new groovy.json.JsonSlurper().parseText(jwtResponse.getContent())
					  env.JWTTOKEN = "Bearer ${jwtObject.jwt}"
				  }
				}
			echo "${env.JWTTOKEN}"
			}
		}
        stage("Pushing Image"){
            steps {
                script {
                    docker.withRegistry("", 'dockerhub-creds'){
                        //dockerImage.push("${env.BUILD_NUMBER}")
						//dockerImage.push("latest")
						dockerImage.push("latest")
                    }
                }
            }
        }
    }
	post {
		always {
		  deleteDir() /* cleanup the workspace */
		}
	}
}