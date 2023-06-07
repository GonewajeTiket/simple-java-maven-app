node {
    agent any
    triggers {
        pollSCM('H/2 * * * *')
    }
    
    stage('Checkout') {
        checkout([$class: 'GitSCM', 
                  branches: [[name: '*/master']],
                  userRemoteConfigs: [[url: 'https://github.com/GonewajeTiket/simple-java-maven-app']]])
    }
    
    stage('Build') {
        docker.image('maven:3.9.0-eclipse-temurin-11').inside('-v /root/.m2:/root/.m2') {
            sh 'mvn -B -DskipTests clean package'
        }
    }

    stage('Test') {
        docker.image('maven:3.9.0-eclipse-temurin-11').inside('-v /root/.m2:/root/.m2') {
            try {
                sh 'mvn test'
            } finally {
                junit 'target/surefire-reports/*.xml'
            }
        }
    }

    stage('Deliver') {
        // Assuming deliver.sh script is in the same directory as the Jenkinsfile
        docker.image('maven:3.9.0-eclipse-temurin-11').inside('-v /root/.m2:/root/.m2') {
            sh './jenkins/scripts/deliver.sh'
        }

    }
}
