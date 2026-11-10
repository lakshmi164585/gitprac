This is a Jenkins Declarative Pipeline for a Maven-based Java application. It has three stages: Build, Test and Deploy.
1. Line-by-line explanation
pipeline {
    agent any


- pipeline: Defines a Jenkins pipeline.
- agent any: Jenkins can execute the pipeline on any available agent.
stages {


Defines the collection of stages in the pipeline.

Stage 1: Build
stage('Build') {
    steps {
        sh 'mvn clean package'
    }
}


- mvn clean: Removes previously generated build files.
- mvn package: Compiles the code, runs tests by default and packages the application, usually into a JAR or WAR file.
- sh: Executes a shell command on a Linux-based Jenkins agent.
Stage 2: Test
stage('Test') {
    steps {
        sh 'mvn test'
    }
}


Runs the Maven test phase to execute unit tests.

Note: Maven's package phase already runs the test phase by default, so this pipeline runs tests twice unless you configure otherwise.

Stage 3: Deploy
stage('Deploy') {
    steps {
        sh 'deploy.sh'
    }
}


Executes a deployment shell script. That script must exist in the workspace and have execution permission.
chmod +x deploy.sh


2. Pipeline execution flow
Jenkins Pipeline


Build – Maven package

Compile and package application


Test – Maven test

Execute unit tests


Deploy – deploy.sh

Deploy application to target environment


Pipeline complete if all stages succeed

3. Improved interview-ready version
You can avoid running Maven tests twice by using -DskipTests during packaging and running tests separately:
pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Deploy') {
            steps {
                sh 'chmod +x deploy.sh'
                sh './deploy.sh'
            }
        }
    }
}


4. Interview questions
Q1. What happens if the Build stage fails?

Jenkins marks the pipeline as failed and normally skips the remaining stages.

Q2. What happens if tests fail?

The pipeline stops before deployment, preventing an unsuccessful build from being deployed.

Q3. What is agent any?

It tells Jenkins to execute the pipeline on any available agent that meets the job's requirements.

Q4. What is the purpose of deploy.sh?

It automates deployment tasks such as transferring artifacts, updating application files, restarting services and validating the application.

Q5. How would you deploy this application to AWS EC2?

The deployment script could securely transfer the JAR/WAR artifact to EC2, update the application and restart the relevant service. Credentials should be managed securely through Jenkins credentials or an approved AWS identity mechanism.

Remember for interviews: Build → Test → Deploy. Never deploy an artifact if the required build or test stages have failed.
