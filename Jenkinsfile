node{
    
     def tag, dockerHubUser, containerName, httpPort = ""
    environment {
        DOCKERHUB_CREDENTIALS = credentials('dockerHubAccount')
    }
    
    stage('Initialized Environment'){
        dockerHubUser = 'mmehdizadeh7777'
        tag="1.0"
        containerName="bankingapp"
        httpPort="8989"      
    }
    
    
    stage('Code Checkout'){
        try{
            checkout scm
        }
        catch(Exception e){
            echo 'Exception occured in Git Code Checkout Stage'
            currentBuild.result = "FAILURE"
        }
    }
    
    stage('Maven Build'){
        sh "/opt/apache-maven-3.6.3/bin/mvn clean package"   
           
    }
    
    stage('Docker Image Build'){
        echo 'Creating Docker image'
        sh "docker build -t $dockerHubUser/$containerName:$tag --pull --no-cache ."
    }  
	
    stage('Publishing Image to DockerHub'){
        echo 'Pushing the docker image to DockerHub'
        //sh "echo $DOCKERHUB_CREDENTIALS_PSW | docker login -u $DOCKERHUB_CREDENTIALS_USR --password-stdin"
        withCredentials([usernamePassword(credentialsId: 'dockerHubAccount',
                     usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD')]) {
 
                     sh 'echo $PASSWORD'
                    echo "${env.USERNAME}"
            
            sh "docker login -u $USERNAME -p $PASSWORD"
            sh "docker push $USERNAME/$containerName:$tag"
            echo "Image push complete"
        }


    }    
	
	stage('Ansible Playbook Execution'){
		//sh "ansible-playbook -i inventory.yaml kubernetesDeploy.yaml -e httpPort=$httpPort -e containerName=$containerName -e dockerImageTag=$dockerHubUser/$containerName:$tag" --key-file ~/.ssh/id_rsa


                                echo "deploying banking pod in Kubernetes"
                                script{
                                    def ansiblecmd = "ansible-playbook -i inventory.yaml kubernetesDeploy.yaml -e httpPort=$httpPort -e containerName=$containerName -e dockerImageTag=$dockerHubUser/$containerName:$tag"
                                    sshagent(['master-key']) {
                                      sh "ssh -o StrictHostKeyChecking=no root@192.168.85.20 ${ansiblecmd}"

                                    }
                                }           
	}
}


