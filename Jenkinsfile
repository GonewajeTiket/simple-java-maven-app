node {
    
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


    stage('Deploy') {
        input message: 'Lanjutkan ke tahap Deploy?'
        // Copy jar and pom file to ec2 instance then ssh to run a deliver script
        sh 'scp -r -i /var/jenkins_home/workspace/ssh-gonewaje.pem /var/jenkins_home/workspace/submission-cicd-pipeline-gonewaje/pom.xml /var/jenkins_home/workspace/submission-cicd-pipeline-gonewaje/target /var/jenkins_home/workspace/submission-cicd-pipeline-gonewaje/jenkins/scripts/deliver.sh ec2-user@13.212.194.223:/home/ec2-user'
            sh '''
                ssh -i /var/jenkins_home/workspace/ssh-gonewaje.pem ec2-user@13.212.194.223 '
                    'sh deliver.sh
                '''
            sleep(time: 60)
    }
}