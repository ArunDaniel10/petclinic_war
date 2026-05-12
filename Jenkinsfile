pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ArunDaniel10/petclinic_war.git',
                    credentialsId: 'github-cred'
            }
        }

        stage('Compile') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv('sonar') {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Build WAR') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Upload to Nexus') {
            steps {
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: '18.209.29.3:8081',
                    groupId: 'com.petclinic',
                    version: '1.0',
                    repository: 'maven-releases',
                    credentialsId: 'Nexus',
                    artifacts: [
                        [
                            artifactId: 'petclinic',
                            classifier: '',
                            file: 'target/petclinic.war',
                            type: 'war'
                        ]
                    ]
                )
            }
        }

        stage('Deploy to Tomcat') {
            steps {
                deploy adapters: [tomcat9(
                    credentialsId: 'tomcat',
                    path: '',
                    url: 'http://54.81.200.201:8080'
                )],
                war: 'target/petclinic.war'
            }
        }
    }

    post {

        success {
            mail to: 'arun11raja@gmail.com',
                 subject: 'PetClinic Pipeline Success',
                 body: 'PetClinic WAR built and deployed successfully.'
        }

        failure {
            mail to: 'arun11raja@gmail.com',
                 subject: 'PetClinic Pipeline Failed',
                 body: 'Pipeline execution failed. Check Jenkins console output.'
        }
    }
}
