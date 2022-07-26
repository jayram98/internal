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
                git branch: 'main',
                    url: 'https://github.com/jayram98/internal.git'
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
	stage ('K8S Deploy') {
        steps {
            script {
		    sh "ls -ltha"
		    //sh "kubectl apply -f "k8s-deployment.yaml""
                kubernetesDeploy(
		    	
                    configs: 'k8s-deployment.yaml,internal-deployment.yaml,internal-namespace.yaml,internal-service.yaml',
                    kubeconfigId: 'aks',
                    enableConfigSubstitution: true
		)}
	}
	}
        
    
}
}
