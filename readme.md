# Pushing images to ECR

## Jenkins Plugins
- [CloudBees AWS Credentials](https://plugins.jenkins.io/aws-credentials/)
- [Amazon ECR](https://plugins.jenkins.io/amazon-ecr/)
- [Docker Pipeline](https://plugins.jenkins.io/docker-workflow/)


## Tutorial

#### Get AWS Access Key (probably got one already that pedro sent us)
AWS console -> IAM -> Users -> Security Credentials -> Create Access Key

#### Create ECR Repository (i've created one for testing)
AWS console -> ECR -> Create Repository


#### Add AWS Credentials to Jenkins

On Jenkins, Manage -> Manage Credentials -> Store ->  Add Credentials

```

Kind - AWS credentials
ID - anything
Access Key ID - Access Key ID from creds
Secret Access Key - Secret Access Key from creds

```


#### Create a Pipeline (defined in this repo in **Jenkinsfile**

```
pipeline {
    agent any
    options {
        skipStagesAfterUnstable()
    }
    stages {
         stage('Clone repository') { 
            steps { 
                script{
                checkout scm
                }
            }
        }

        stage('Build') { 
            steps { 
                script{
                 app = docker.build("jenkins-test") // build the image, the argument should be the name used for the ecr repo
                }
            }
        }

        stage('Deploy') {
            steps {
                script{
                        docker.withRegistry('https://358682810880.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-2:aws-credentials') { // push built image to ecr 
                    		app.push("latest")
                    	}
                }
            }
        }
    }
}
```

docker.build("ecr-repository-name")
docker.withRegistry("aws-ecr-url", ...)
