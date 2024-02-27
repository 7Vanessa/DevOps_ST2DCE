pipeline {
    agent any

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/7Vanessa/DevOps_ST2DCE.git'
            }
        }
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    def dockerImage = docker.build("st2dce-proj-image:${BUILD_ID}")
                    docker.withRegistry('https://registry.hub.docker.com', 'credentials-id') {
                        dockerImage.push()
                    }
                }
            }
        }
        stage('Deploy to Development') {
            steps {
                sh 'kubectl apply -f k8s/dev-deployment.yaml'
            }
        }
        stage('Validate Deployment') {
            steps {
                sh 'curl http://localhost:8080'
            }
        }
        stage('Deploy to Production') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                sh 'kubectl apply -f k8s/prod-deployment.yaml'
            }
        }
    }
}
