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
					  def jwtResponse = httpRequest acceptType: 'APPLICATION_JSON', contentType: 'APPLICATION_JSON', validResponseCodes: '200', httpMode: 'POST', ignoreSslErrors: true, consoleLogResponseBody: true, requestBody: json, url: "http://admin.smarthought.in/api/auth"
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
		
		stage('Delete old Stack') {
		  steps {
			script {

			  // Get all stacks
			  String existingStackId = ""
			  if("true") {
				def stackResponse = httpRequest httpMode: 'GET', ignoreSslErrors: true, url: "http://admin.smarthought.in/api/stacks", validResponseCodes: '200', consoleLogResponseBody: true, customHeaders:[[name:"Authorization", value: env.JWTTOKEN ], [name: "cache-control", value: "no-cache"]]
				def stacks = new groovy.json.JsonSlurper().parseText(stackResponse.getContent())
				
				stacks.each { stack ->
				  if(stack.Name == "BOILERPLATE") {
					existingStackId = stack.Id
				  }
				}
			  }

			  if(existingStackId?.trim()) {
				// Delete the stack
				def stackURL = """
				  http://admin.smarthought.in/api/stacks/$existingStackId
				"""
				httpRequest acceptType: 'APPLICATION_JSON', validResponseCodes: '204', httpMode: 'DELETE', ignoreSslErrors: true, url: stackURL, customHeaders:[[name:"Authorization", value: env.JWTTOKEN ], [name: "cache-control", value: "no-cache"]]

			  }

			}
		  }
		}		
		//add the new deploy stage
    }
	post {
		always {
		  deleteDir() /* cleanup the workspace */
		}
	}
}