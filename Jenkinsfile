node {
    properties([
        pipelineTriggers([
            cron('*/2 * * * *')
        ])
    ])

    stage('Build') {
        agent {
            docker {
                image 'maven:3.9.0-eclipse-temurin-11'
                args '-v /root/.m2:/root/.m2'
            }
        }
        steps {
            sh 'mvn -B -DskipTests clean package'
        }
    }

    stage('Test') {
        agent {
            docker {
                image 'maven:3.9.0-eclipse-temurin-11'
                args '-v /root/.m2:/root/.m2'
            }
        }
        steps {
            sh 'mvn test'
        }
        post {
            always {
                junit 'target/surefire-reports/*.xml'
            }
        }
    }

    stage('Deliver') {
        agent any
        steps {
            sh './jenkins/scripts/deliver.sh'
        }
    }
}