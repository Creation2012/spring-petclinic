pipeline {
    agent any
    stages {
        stage('checkstyle') {
            steps {
                sh "./mvnw clean checkstyle:checkstyle"
            }
            post {
                always {
                    junit '**/target/checkstyle-result.xml'
                    archiveArtifacts './target/site/'
                }
            }
        }
        stage('test') {
            steps {
                sh "./mvnw clean test"
            }
        }
        stage('build') {
            steps {
                sh "./mvnw -DskipTests clean package"
            }
            post {
                success {
                    archiveArtifacts './target/spring-petclinic-*.jar'
                }
            }
        }
        stage('image') {
            steps {
                sh "echo create docker image"
            }
        }
    }
}
