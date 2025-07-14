#hands on pipeline upto push image to dockerhub

pipeline {
    agent any
    tools {
        maven 'maven3'
    }
    stages {
        stage('clean workspace') {
            steps {
              cleanWs()
            }
        }
         stage('Git checkout') {
            steps {
              git branch: 'main', credentialsId: 'git-cred', url: 'https://github.com/subhadra3n/java-maven-app.git'
            }
        }
        stage ('maven'){
            steps{
                 sh 'mvn clean package'
            }
        }
       stage('Docker Build') {
            steps {
                script {
                    def imageName = 'anuragk21/maven-cloudaseem-app:latest'
                    echo "Building Docker image: ${imageName}"
                    sh "docker build -t ${imageName} ."
                }
            }
        }
        stage('Docker Push') {
            steps {
                script {
                    def imageName = 'anuragk21/maven-cloudaseem-app:latest'
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub-creds',  // Docker Hub credentials stored in Jenkins
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )]) {
                        echo "Pushing Docker image to Docker Hub..."
                        sh """
                            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                            docker push ${imageName}
                        """
                    }
                }
            }
             
        }
        
        
    }
    
}









____________________________________________________________________________________________________----
# Project Setup using below tools

1) Maven - Build tool
2) Git Hub - SCM
3) Jenkins - CICD TOOL
4) Docker _ container


# Step-1 : Jenkins Server Setup in Linux VM #

1) Create Ubuntu VM using AWS EC2 (t2.medium) <br/>
2) Enable 8080 Port Number in Security Group Inbound Rules
3) Connect to VM using MobaXterm
4) Install Java

```
sudo apt update
sudo apt install fontconfig openjdk-17-jre
java -version
```

3) Install Jenkins
```
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update
sudo apt-get install jenkins
```
4) Start Jenkins

```
sudo systemctl enable jenkins
sudo systemctl start jenkins
```

5) Verify Jenkins

```
sudo systemctl status jenkins
```
	
6) Access jenkins server in browser using VM public ip

```
http://public-ip:8080/

```

7) Copy jenkins admin pwd

```
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```
	   
8) Create Admin Account & Install Required Plugins in Jenkins
     aseem
      pipline two ways
      Declarative pipeline-  simplified way
      Scripted Pipeline - Groovy 

      Declarative Pipeline:
      Structured and simplified way to define pipelines in Jenkins.
      Uses predefined syntax with specific keywords.
      Great for standardizing pipeline definitions and simpler requirements.
      Offers less flexibility compared to Scripted Pipeline.

      Scripted Pipeline:
      Allows writing pipelines as Groovy scripts.
      Provides more flexibility and control with Groovy scripting.
      Suitable for complex scenarios and advanced control flow.
      Can be verbose and complex compared to Declarative Pipeline.

## Step-2 : Configure Maven as Global Tool in Jenkins ##

1) Manage Jenkins -> Tools -> Maven Installation -> Add maven <br/>

## Step-3 : Setup Docker in Jenkins ##
```
curl -fsSL get.docker.com | /bin/bash
sudo usermod -aG docker jenkins
sudo usermod -aG docker ubuntu
sudo systemctl restart jenkins
sudo docker version
```

# Step - 4 : Create Jenkins Job #

- **Stage-1 : Clone Git Repo** <br/> 

- **Stage-2 : Maven Build** <br/>

- **Stage-3 : Create Docker Image** <br/>


- **Stage-4 : Create Docker Container** <br/>
	
# Step - 5 : Trigger Jenkins Job #

# Step - 6 : Enable host port in security group inbound rules #

# Step - 7 : Access Application in Browser #

- **We should be able to access our application** <br/>

URL : http://public-ip:port/
	

	
# Step - 10 : After your practise, delete resources we have used in AWS Cloud to avoid billing ##
