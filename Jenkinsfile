pipeline {
    agent any
    environment {
        PROJECT_ID_DEV = 'terraformproject01'
        CLUSTER_NAME_DEV = 'teams-dev-us-central1'
        LOCATION_DEV = 'us-central1'
        CREDENTIALS_ID_DEV = 'terraformproject01'
        
        PROJECT_ID_UAT = 'mims-324019'
        CLUSTER_NAME_UAT = 'cluster-2'
        LOCATION_UAT = 'us-east1-b'
        CREDENTIALS_ID_UAT = 'Mims'
    }
    stages {
        stage("Checkout code") {
            steps {
                checkout scm
            }
        }
        stage("Build image") {
            steps {
                script {
                    
                    myapp = docker.build("kiranug/hello:${env.BUILD_ID}")
                    
                }
            }
        }
        stage("Push image") {
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                            myapp.push("latest")
                            myapp.push("${env.BUILD_ID}")
                    }
                }
            }
        }        
        stage('Deploy to GKE DEV') {
            steps{
                sh "sed -i 's/hello:latest/hello:${env.BUILD_ID}/g' deployment0.yaml"
                step([$class: 'KubernetesEngineBuilder', projectId: env.PROJECT_ID_DEV, clusterName: env.CLUSTER_NAME_DEV, location: env.LOCATION_DEV, manifestPattern: 'deployment.yaml', credentialsId: env.CREDENTIALS_ID_DEV, verifyDeployments: true])
            }
        }
    }    
}
