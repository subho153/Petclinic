pipeline {
    agent any
    tools{
        maven 'maven3'
        jdk 'jdk17'
    }
    environment {
        SCANNER_HOME=tool 'sonar-scanner'
    }
    stages {
        stage('Git') {
            steps {
                git branch: 'main', url: 'https://github.com/subho153/Petclinic.git'
            }
        }
        stage('Code Compile') {
            steps {
                sh 'mvn compile'
            }
        }
        stage('Code Test') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Code Build') {
            steps {
                sh 'mvn package'
            }
        }
        stage('Trivy Scan') {
            steps {
                sh 'trivy fs --format table -o fs.html .'
            }
        }
        stage("Sonarqube Analysis "){
            steps{
                withSonarQubeEnv('sonar') {
                    sh ''' $SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=PetclinicWeb \
                    -Dsonar.java.binaries=. \
                    -Dsonar.projectKey=PetclinicWeb '''
    
                }
            }
        }
        stage('Docker Build & push') {
            steps {
                script{
                  withDockerRegistry(credentialsId: 'dockerconn') {
                        sh 'docker build -t newwebspringbootapp .'
                        sh 'docker tag newwebspringbootapp dockerhubdemosubha1234/javawebapplication'
                        sh 'docker push dockerhubdemosubha1234/javawebapplication'
                        sh 'docker run -d --name newwebspringbootapp -p 8082:8082 dockerhubdemosubha1234/javawebapplication'
                             
                    }  
                }
            }
        }
    }
}
