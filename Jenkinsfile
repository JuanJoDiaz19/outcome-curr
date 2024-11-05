pipeline {
    agent any

    tools {
        jdk 'OpenJDK-17'
        maven 'maven3'
    }

    stages {
        stage('Checkout') {
            steps {
                git url: 'https://github.com/JuanJoDiaz19/outcome-curr.git', branch: 'main'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean install -DskipTests -U'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Add Dokku Host Key') {
            steps {
                sh '''
                    mkdir -p ~/.ssh
                    ssh-keyscan -H ec2-18-191-161-171.us-east-2.compute.amazonaws.com >> ~/.ssh/known_hosts
                '''
            }
        }

        stage('Deploy to Dokku') {
            steps {
                sshagent(['ssh-dokku']) {
                    sh "git remote add dokku dokku@ec2-18-191-161-171.us-east-2.compute.amazonaws.com:outcurr || true"
                    sh "git push dokku main -f"
                }
            }
        }
    }

    // post {
    //     always {
    //         junit '**/target/surefire-reports/*.xml'
    //         jacoco execPattern: '**/target/*.exec'
    //         cleanWs() // Cleans up the workspace after build completion
    //     }
    // }
}