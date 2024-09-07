pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven3'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Powerfulrider/FullStack-Blogging-App.git'
            }
       }
    
        stage('Compile') {
            steps {
               sh  'mvn compile'
            }
       }
    
        stage('Test') {
            steps {
               sh 'mvn test'
            }
       }
    
        stage('Trivvy FS Scan') {
            steps {
               sh 'trivy fs --format table -o fs.html .'
            }
       }
    
        stage('Sonarcube scan') {
            steps {
               withSonarQubeEnv('sonar-server') {
                    sh '''$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectName=Blogging-app -Dsonar.projectKey=Blogging-app \
                        -Dsonar.java.binaries=target '''
                }
            }
       }
    
        stage('maven build') {
            steps {
               sh 'mvn package'
            }
       }
    
        stage('Publisg Artifact') {
            steps {
               withMaven(globalMavenSettingsConfig: 'maven-settings', jdk: 'jdk17', maven: 'maven3', mavenSettingsConfig: '', traceability: true) {
                    sh 'mvn deploy'
                }
            }
       }
       
       stage('docker build') {
            steps {
                script {
               withDockerRegistry(credentialsId: 'docker-creds') {
                    sh 'docker build -t powerfulrider/bloggingapp:latest .'
                    }
                }
            }
       }
       
       stage('Trivy Docker Scan') {
            steps {
               sh 'trivy image --format table -o image.html powerfulrider/bloggingapp:latest'
            }
       }
       
       stage('Docker push') {
            steps {
               script {
               withDockerRegistry(credentialsId: 'docker-creds') {
                    sh 'docker push powerfulrider/bloggingapp:latest'
                    }
                }
            }
       }
    }
}
