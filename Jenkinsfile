pipeline {
    agent any
    stages {
        stage('checkout') {
            environment {
                COMMIT_SHA = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
            }
            steps {
                script {
                    // The Git plugin will automatically check out the code
                    // You can access the commit SHA using the GIT_COMMIT environment variable
                    echo "Commit SHA: ${env.GIT_COMMIT}"
                }
            }
        }

        stage('checkstyle') {
            steps {
                sh "./mvnw clean checkstyle:checkstyle"
            }
            post {
                always {
                    archiveArtifacts 'target/*.xml'
                    archiveArtifacts 'target/site/'
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
                    archiveArtifacts "target/${env.APP_NAME}-*.jar"
                }
            }
        }
        stage('image to mr') {
            steps {
                sh 'mkdir target/dependency; cd target/dependency; jar -xf ../*.jar'
                script {
                    docker.withRegistry("${env.DOCKER_SERVER_mr}", 'repository_login_creds') {
                        def app = docker.build("${env.APP_NAME}:${env.GIT_COMMIT}")
                        app.push("${env.GIT_COMMIT}")
                        app.push("latest")
                    }
                }
            }
        }
        stage('image to main') {
            when {
                branch 'main'
            }
            steps {
                script {
                    docker.withRegistry("${env.DOCKER_SERVER_main}", 'repository_login_creds') {
                        def app = docker.build("${env.APP_NAME}:${env.GIT_COMMIT}")
                        app.push("${env.GIT_COMMIT}")
                        app.push("latest")
                    }
                }
            }
        }
    }
}
