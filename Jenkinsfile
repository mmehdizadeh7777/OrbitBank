node{
    
     def tag, dockerHubUser, containerName, httpPort = ""
    
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
        echo "deploying banking pod in Kubernetes"
		sh "ansible-playbook -i inventory.yaml kubernetesDeploy.yaml -e httpPort=$httpPort -e containerName=$containerName -e dockerImageTag=$dockerHubUser/$containerName:$tag --key-file ~/.ssh/id_rsa"        
	}
}


