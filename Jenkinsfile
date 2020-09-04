pipeline {
    agent any
    environment {
        PROJECT_ID = 'CICD'
        CLUSTER_NAME = 'cluster-1'
        LOCATION = 'us-central1-c'
        CREDENTIALS_ID = 'CICD'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'b300aa55-6dcd-4373-9ba9-e9792d46c772', url: 'https://github.com/musapple/DevopsChallenge.git']]])
            }
        }
        stage("Build image") {
            steps {
                script {
                    myapp = docker.build("musapple/hello:${env.BUILD_ID}")
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', '15baf096-1836-4b26-bb5d-6873f49370f5') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID, clusterName: env.CLUSTER_NAME, location: env.LOCATION, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID, verifyDeployments: true])
            }
        }
    }    
}