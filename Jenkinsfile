pipeline {
    agent any
    stages {
        stage('Build maven') {
            steps {
                sh 'pwd'
                sh 'mvn clean install package'
            }
        }
        stage('Copy Artifact') {
            steps {
                sh 'pwd'
                sh 'cp -r target/*.jar docker'
            }
        }
        stage('Run Tests') {
            steps {
                sh 'mvn test'
            }
        }
        stage('Build doc image') {
            steps {
                script {
                    def customImage = docker.build("ajaz9565/petclinic:${env.BUILD_NUMBER}", "./docker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        customImage.push()
                    }
                }
            }
        }
        stage('Build on k8s'){
            steps {
                withKubeConfig([credentialsId: 'kubeconfig']){
                    sh 'pwd'
                    sh 'cp -R helm/* .'
                    sh 'ls -ltrh'
                    sh 'pwd'
                    sh '/usr/local/bin/helm upgrade --install petclinic-app petclinic --set image.repository=ajaz9565/petclinic --set image.tag=${BUILD_NUMBER}'
                }
            }
        }
    }
}
