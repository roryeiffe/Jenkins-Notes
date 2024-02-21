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

### Examples from 2/21:
- https://github.com/roryeiffe/hello-world-jenkins
- https://github.com/roryeiffe/simple-java-build 