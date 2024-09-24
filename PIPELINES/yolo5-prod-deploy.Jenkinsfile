pipeline {
    agent {
        label 'general'
    }

    parameters {
        string(name: 'SERVICE_NAME', defaultValue: '', description: '')
        string(name: 'IMAGE_FULL_NAME_PARAM', defaultValue: '', description: '')
    }

    stages {
        stage('Git setup') {
            steps {
                sh 'git checkout -b main || git checkout main'
            }
        }
        stage('Update YAML manifests') {
            steps {
                sh '''
                cd K8S/$SERVICE_NAME
                sed -i "s|image: .*|image: ${IMAGE_FULL_NAME_PARAM}|" yolo5-deployment.yaml
                git add yolo5-deployment.yaml
                git commit -m "Jenkins deploy $SERVICE_NAME $IMAGE_FULL_NAME_PARAM"
                '''
            }
        }
        stage('Git push') {
            steps {
               withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'GITHUB_USERNAME', passwordVariable: 'GITHUB_TOKEN')]) {
                 sh '''
                 git pull --rebase https://$GITHUB_TOKEN@github.com/EhabOrabi/PolybotInfra.git main
                 git push https://$GITHUB_TOKEN@github.com/EhabOrabi/PolybotInfra.git main
                 '''
               }
            }
        }
    }
    post {
        cleanup {
            cleanWs()
        }
    }
}

