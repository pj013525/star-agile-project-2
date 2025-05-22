pipeline {
    agent any

    stages {
        stage('Git checkout') {
            steps {
                git branch: 'master', url: 'https://github.com/pj013525/star-agile-project-2.git'
            }
        }

        stage('Compilation') {
            steps {
                sh 'mvn compile'
            }
        }

        stage('Testing') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Scanner') {
            steps {
                withSonarQubeEnv('sonarqube-server') {
                    sh 'mvn org.sonarsource.scanner.maven:sonar-maven-plugin:5.0.0.4389:sonar'
                }
            }
        }

        stage('Packing') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Pushing Artifact to Nexus') {
            steps {
               nexusArtifactUploader artifacts: [[artifactId: 'medicure', classifier: '', file: 'target/medicure-0.0.1-SNAPSHOT.jar', type: 'jar']], credentialsId: 'nexus-creds', groupId: 'com.project.staragile', nexusUrl: '98.130.31.85:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'Health-care', version: '0.0.1-SNAPSHOT'
            }
        }

        stage('Build the Docker Image') {
            steps {
                script {
                    sh 'docker build -t pj013525/health-care:v1 .'
                    sh 'docker images'
                }
            }
        }

        stage('Push Image to DockerHub') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-details', variable: 'dockerhubPassword')]) {
                    sh "docker login -u pj013525 -p ${dockerhubPassword}"
                    sh 'docker push pj013525/health-care:v1'
                }
            }
        }
    }
}

