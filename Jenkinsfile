pipeline {
    agent {
        docker {
            image 'maven:3.8.1-adoptopenjdk-11'
            args '-v /root/.m2:/root/.m2'
        }
    }
    options {
            skipStagesAfterUnstable()
        }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') {
                    steps {
                        sh 'mvn test'
                    }
                    post {
                        always {
                            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                            junit 'target/surefire-reports/*.xml'
                        }
                    }
        }
        stage('Deliver') {
                    steps {
                        sh './jenkins/scripts/deliver.sh'
                    }
         }
        stage('Deploy') {
                     steps {
                        timeout(time: 3, unit: 'MINUTES') {
                            retry(5) {
                                sh './jenkins/scripts/sample.sh'
                            }
                        }
                     }
        }

    }
    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'This will run only if successful'
        }
        failure {
            echo 'This will run only if failed'
        }
        unstable {
            echo 'This will run only if the run was marked as unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}