## Jenkins

- CICD pipeline, DevOps
- Automated or semi-automated part of the pipeline
- Can be configured to detect changes and build based on those changes
- Manual stops

### CICD
- Continuous Integration
- Continuous Delivery - manual intervention/check before you push it off to production
- Continuous Deployment - automated from start to finish 

### Jenkins Pipeline
Also referred to as "Pipeline" contains the suite of plugins and the goal is to implement a pipeline for our software project.

### Jenkinsfile
A file that contains the instructions for all the steps in our development cycle so it can be tweaked and replicated across other projects

Making changes when necessary.

Useful for micro-services and projects that leverage cloud services.

### Master and Agent
Jenkins has a master-agent architecture where the master is the brain of the operation. It is responsible for managing and distributing the different processes to its worker nodes or agents. The master serves as the central point of control. When we interface with localhost:8080 we are interfacing with the master.

The agents are the works that work based on what the master instructs it to do. Works as a distributed system so good for scaling.

Master is equivalent to controller.
Agent is equivalent to node/slave.

### Jenkins Plugins
Plugins give us additional functionality. Some can be installed when first setting up Jenkins. 



### Listening for Changes
1. Navigate to pipeline
1. Click "Configure"
1. Locate "Scan Repository Triggers" and check the box "Periodically if not otherwise run"
1. Select a time interval and save. Jenkins should scan for changes based on this interval and perform builds when new commits are pushed.
1. 

### Examples from 2/21:
- https://github.com/roryeiffe/hello-world-jenkins
- https://github.com/roryeiffe/simple-java-build 

### Accessing Private Repos
In order to access a private repo, you must set up credentials in Jenkins. 

First, create a PAT (Personal Access Token) in Github by following these steps: https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens. 

Make sure to copy the PAT. 

In Jenkins, go to Dashboard -> Manage Jenkins -> Credentials

Add a new set of credentials:
1. Kind: Username with password
1. Scope: Global
1. Username: Github username
1. Password: PAT
1. Click "Create". 

When configuring the source in your pipeline, just add the credentials that you just configured. 


## Jenkins Run after every Commit
How can we get our pipeline to run whenever there is a new commit?

We saw that we can set an interval for every x minutes/hours in order to check for updates. But we want a way to continuously monitor.

### Github Webhooks
On Github repo, go to settings, find Webhooks on the left sidebar and click Add Webhook. 

Select content-type to be application/json and leave events as just pushes. 

For the payload url, we can't put in localhost so we will need some service to expose that endpoint publicly. 

### Ngrok
Can download [here](https://ngrok.com/download) and follow the steps.

Can open the program or run ngrok commands from command line.

Can run ngrok http port-num, ex: ngrok http 8080. 

Should see a public endpoint that we can enter in our browser and see our Jenkins instance. 

Now, we can go back to Github to the webhook configuration, enter in the payload url public-endpoint/github-webhook/

### Configuring the Pipeline in Jenkins
Add a new item, choose Pipeline type. Configure the Github link.

Under build triggers, check the box "GitHub hook trigger for GITScm polling". 

Under pipeline, choose where we want to get the Jenkinsfile (Pipeline Script from SCM). Then, just configure the repo link, branch, and location of Jenkinsfile (and credentials if necessary). 

Hit save + apply. Try committing/pushing to the repo and note if the build starts automatically. 

### Multiple Jenkinsfiles in different branches
If you have a project with multiple Jenkinsfiles, you can separate them into different branches. Each branch can have a Jenkinsfile with different steps. When creating the pipeline in Jenkins, just make sure to select Multi-Branch pipeline and configure the Github source accordingly (specify the repo link, path to Jenkinsfiles, credentials if necessary).

### Simple Java Build
```Groovy
pipeline {
    agent { 
        docker { 
            image 'maven:3.9.6-eclipse-temurin-11-alpine'
        } 
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Post-Build') {
          steps {
            archiveArtifacts 'target/demo-0.0.1-SNAPSHOT.jar'
          }
        }
    }
}
```

### On AWS
1. Log in to the AWS console as the root user. 
1. Create an IAM user that has full access to creating an AWS Elastic Beanstalk instance.
1. Create an access key for your user and note it down.

### Jenkins
1. Install the AWS Elastic Beanstalk plugin (AWSEB)
  1. This should install necessary dependencies
1. Add credentials
  1. Manage Jenkins -> Credentials -> System -> Global Credentials
  1. Choose "AWS Credentials" and fill in your access key from earlier
1. Pipeline

### AWS Set up Elastic Beanstalk
1. Create a new environment
  1. Pick a name
  1. Platform -> Java 11
  1. Code -> Sample App
  1. Review other settings and create


### Jenkinsfile

```groovy
// Define pipeline stages
pipeline {
    agent { 
        docker { 
            image 'maven:3.9.6-eclipse-temurin-11-alpine'
        } 
    }

    stages {
        // Checkout code from Github
        stage('Checkout Code') {
            steps {
                git branch: 'master',
                    credentialsId: 'aa74eeb8-0e1b-4fb2-bb4b-fe28cde71a8c', // Replace with your credential ID
                    url: 'https://github.com/roryeiffe/web-app-2-2024' // Replace with your repo URL
            }
        }

        // Build the application with Maven
        stage('Build with Maven') {
            steps {
                sh 'mvn clean install'
            }
        }

        // Deploy to Elastic Beanstalk
        stage('Deploy to Elastic Beanstalk') {
            steps {
                step($class: 'AWSEBDeploymentBuilder',
                    credentialId: "aws-cred",
                    awsRegion: "us-east-1",
                    applicationName: "jenkins-app",
                    environmentName: "Jenkins-app-env",
                    keyPrefix: "jenkins-app/builds",
                    rootObject: "target/demo-0.0.1-SNAPSHOT.jar",
                    versionLabelFormat: "-${BUILD_NUMBER}",)
                }
            
        }
    }
}
```

#### Troubleshooting
- Make sure the web app has a default endpoint set up (/)
- Double-check versions, I've had luck with Java 11 (will probably need to down-grade Spring version to 2-something)



## Practice Questions
1. What is Jenkins, and what is its purpose?
1. Explain the concept of Continuous Integration (CI) and Continuous Delivery (CD). How does Jenkins fit into these practices?
1. Describe the core components of Jenkins: Master, Slave (Agent), Job, and Pipeline.
1. Explain the different ways you can trigger a Jenkins job.
1. How can you secure sensitive information like credentials within your Jenkins pipelines?
1. What are some plugins we used? And what is their purpose?
1. How can we monitor/troubleshoot our Jenkins builds?
1. What are some of the types of items we can create in Jenkins?
1. How could we set up a Jenkins pipeline for a repository with multiple Jenkinsfiles in different branches?
1. Outline the general steps in setting up a pipeline to deploy a web app to some cloud service. 