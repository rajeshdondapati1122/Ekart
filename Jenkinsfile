pipeline {
    agent any
    
    
    tools {
        maven 'maven3'
        jdk 'jdk17'
    }
    
    environment {
        SCANNER_HOME= tool 'sonar-scanner'
    
    }

    stages {
        stage('git check') {
            steps {
                git branch: 'main', url: 'https://github.com/rajeshdondapati1122/Ekart.git'
            }
        }
    
        stage('compile') {
            steps {
                sh 'mvn compile'
            }
        }
        
        stage('unit test') {
            steps {
                sh 'mvn test -DskipTests=true'
            }
        }
        
        stage('sonar analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=EKART -Dsonar.projectName=EKART \
                    -Dsonar.java.binaries=.'''
    
              }
            }
        }
        
        stage('owasp') {
            steps {
                dependencyCheck additionalArguments: ' --scan ./', odcInstallation: 'DC'
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
        
        stage('build') {
            steps {
                sh 'mvn package -DskipTests=true'
            }
        }
        
        stage('deploy to nexust') {
            steps {
                withMaven(globalMavenSettingsConfig: 'global-maven', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh 'mvn deploy -DskipTests=true'
               }
            }
        }
        
        stage('docker build') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker build -t rajeshdondapati309/ekart:latest -f docker/Dockerfile .'
                }
               
   
                }
            }
        }
        
        stage('trivy') {
            steps {
                sh 'trivy image rajeshdondapati309/ekart:latest > trivy-report.txt'
            }
        }
        
        stage('docker push') {
            steps {
                script{
                    withDockerRegistry(credentialsId: 'docker-cred', toolName: 'docker') {
                        sh 'docker push rajeshdondapati309/ekart:latest'
                        sh 'docker run -d -p 8070:8070 --name cont1 rajeshdondapati309/ekart:latest'
                }
               
   
                }
            }
        }
        
        
        
        
    }
}
