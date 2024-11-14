pipeline {
    agent {
        kubernetes {
            label 'shared-agent'
            defaultContainer 'main-container'
            yaml '''
                apiVersion: v1
                kind: Pod
                metadata:
                  labels:
                    jenkins: 'shared-agent'
                spec:
                    serviceAccountName: jenkins
                    containers:
                        - name: main-container
                          image: maven
                          command:
                           - cat
                          tty: true
                '''
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
                sh 'echo ${BRANCH_NAME}'
                sh 'echo ${GIT_BRANCH}'
            }
        }
        stage('Test') {
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
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}
