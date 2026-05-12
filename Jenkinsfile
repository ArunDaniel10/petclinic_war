pipeline {
    agent any

    tools {
        maven 'Maven'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/ArunDaniel10/petclinic_war.git'
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
            version: "1.0-${env.BUILD_NUMBER}",
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
        sh '''
        curl -v -u admin:admin123 \
        -T target/petclinic.war \
        "http://54.81.200.201:8080/manager/text/deploy?path=/petclinic&update=true"
        '''
    }
}
    
    }
}
