pipeline {
    agent {
        label 'Maven-Build-Env' // Use the Maven slave node for this pipeline
    }
    environment {
        // Set Java 17 as default for Jenkins and Maven build
        JAVA_HOME = '/usr/lib/jvm/java-17-amazon-corretto.x86_64'
        PATH = "${JAVA_HOME}/bin:${PATH}"
    }
    stages {
        stage('Validate Project') {
            steps {
                sh 'mvn validate'
            }
        }

        stage('Unit Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Integration Test') {
            steps {
                sh 'mvn verify -DskipUnitTests'
            }
        }

        stage('App Packaging') {
            steps {
                sh 'mvn package'
            }
        }

        stage('Checkstyle Code Analysis') {
            steps {
                sh 'mvn checkstyle:checkstyle'
            }
        }

        stage('SonarQube Inspection') {
            steps {
                script {
                    // Override Java for SonarQube scan to Java 11
                    env.JAVA_HOME = "/usr/lib/jvm/java-11-amazon-corretto.x86_64"
                    env.PATH = "${env.JAVA_HOME}/bin:${env.PATH}"
                }
                sh """mvn sonar:sonar \
                     -Dsonar.projectKey=Maven-JavaWebApp \
                     -Dsonar.host.url=http://172.31.22.101:9000 \
                     -Dsonar.login=ed7f1ae74cf8b693cadbd47043d4b9ed5ef50913"""
            }
        }

        stage("Upload Artifact To Nexus") {
            steps {
                sh 'mvn deploy'
            }
            post {
                success {
                    echo 'Successfully Uploaded Artifact to Nexus Artifactory'
                }
            }
        }
    }
}
