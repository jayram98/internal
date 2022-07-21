pipeline {
    agent any 
    environment {
        registryCredential = 'dockerhub'
        imageName = 'beachcoder/internal'
        dockerImage = ''
        }
    stages {
        stage('Run the tests') {
             agent {
                docker { 
                    image 'node:18-alpine'
                    args '-e HOME=/tmp -e NPM_CONFIG_PREFIX=/tmp/.npm'
                    reuseNode true
                }
            }
            steps {
                echo 'Retrieve source from github' 
                git branch: 'master',
                    url: 'https://github.com/beachedcoder/2022_6_27_devops_internal.git'
                echo 'showing files from repo?' 
                sh 'ls -a'
                echo 'install dependencies' 
                sh 'npm install'
                echo 'Run tests'
                sh 'npm test'
                echo 'Testing completed'
            }
        }
        stage('Building image') {
            steps{
                script {
                    echo 'building image' 
                    dockerImage = docker.build("${env.imageName}:${env.BUILD_ID}")
                    echo 'image built'
                }
            }
            }
	stage('Login') {

			steps {
				script{
               //docker.withRegistry('',registryCredential)
		withCredentials([string(credentialsId: 'dockerpassword', variable: 'dockerpassword')])
					
					{
	        //sh 'docker build -t  .
		sh "docker login -u 'jay899' -p ${dockerpassword} "       
                sh "docker build -t jay899/int:${env.BUILD_ID} ."		    
		sh "docker images"
	        sh "docker login docker.io"
  		sh "docker push jay899/int:${env.BUILD_ID}"
		    //sh 'docker push external:${env.BUILD_ID}' 
	            //sh 'docker push external:${env.BUILD_ID}'
	            
                    //dockerImage.push(jay899/'${env.BUILD_ID}')
		    
		    //docker push ('${env.BUILD_ID}')}		
                    
                    //docker login --username='${dockerHubUser}' --password='${dockerHubPassword}'
                    
                }
			}
		}           
		}
         stage('deploy to k8s') {
             agent {
                docker { 
                    image 'google/cloud-sdk:latest'
                    args '-e HOME=/tmp'
                    reuseNode true
                        }
                    }
            steps {
                echo 'Get cluster credentials'
                sh 'gcloud container clusters get-credentials demo-cluster --zone us-central1-c --project roidtc-june22-u100'
                sh "kubectl set image deployment/internal-deployment events-internal=${env.imageName}:${env.BUILD_ID} --namespace=events"

             }
        }     
        stage('Remove local docker image') {
            steps{
                echo "pending"
                // sh "docker rmi $imageName:latest"
                sh "docker rmi -f ${env.imageName}:${env.BUILD_ID}"
            }
        }
    }
}
