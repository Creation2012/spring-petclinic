pipeline {
    agent any
    stages {
        stage('checkout') {
            environment {
                COMMIT_SHA = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
            }
            when { changeRequest target: "main", comparator: 'EQUALS' }
            steps {
                script {
                    echo "Commit SHA: ${env.GIT_COMMIT}"
                }
            }
        }

        stage('checkstyle') {
            when { changeRequest target: "main", comparator: 'EQUALS' }
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
            when { changeRequest target: "main", comparator: 'EQUALS' }
            steps {
                sh "./mvnw clean test"
            }
        }
        stage('build') {
            when { changeRequest target: "main", comparator: 'EQUALS' }
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
            when { changeRequest target: "main", comparator: 'EQUALS' }
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
                sh 'mkdir target/dependency; cd target/dependency; jar -xf ../*.jar'
                script {
                    docker.withRegistry("${env.DOCKER_SERVER_main}", 'repository_login_creds') {
                        def app = docker.build("${env.APP_NAME}:${env.GIT_COMMIT}","-f Dockerfile.multi")
                        app.push("${env.GIT_COMMIT}")
                        app.push("latest")
                    }
                }
            }
        }
    }
}
