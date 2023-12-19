pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    dir('src/my_go_project') {
                        // Предполагается, что Jenkinsfile находится в корне вашего репозитория
                        script {
                            sh 'go build -o my_binary'
                        }
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {
                    // Предполагается, что Jenkinsfile находится в корне вашего репозитория
                    sh 'curl -v -u admin:Z6CG'$b5BmrbsqM --upload-file src/my_go_project/my_binary http://localhost:8081/repository/my_nexus_repo/my_binary'
                }
            }
        }
    }
}
