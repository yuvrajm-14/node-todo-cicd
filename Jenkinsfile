pipeline {
    agent { label 'dev-server' }

    stages {
        stage("Code Clone") {
            steps {
                echo "Code Clone Stage"
                git url: "https://github.com/yuvrajm-14/node-todo-cicd.git", branch: "master"
            }
        }

        stage("Code Build & Test") {
            steps {
                echo "Code Build Stage"
                sh "docker build -t node-app ."
            }
        }

        stage("Push To DockerHub") {
            steps {
                withVault([vaultSecrets: [
                    [path: 'secret/dockerhub', secretValues: [
                        [envVar: 'dockerHubUser', vaultKey: 'username'],
                        [envVar: 'dockerHubPass', vaultKey: 'password']
                    ]]
                ]]) {
                    sh 'echo $dockerHubPass | docker login -u $dockerHubUser --password-stdin'
                    sh "docker image tag node-app:latest ${dockerHubUser}/node-app:latest"
                    sh "docker push ${dockerHubUser}/node-app:latest"
                }
            }
        }

        stage("Deploy") {
            steps {
                sh "docker compose down && docker compose up -d --build"
            }
        }
    }
}

