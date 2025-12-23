pipeline {
    agent any

    environment {
        K8S_NAMESPACE = "automotive"
        MONGO_APP     = "mongo"
    }

    stages {

        stage('Verify Namespace') {
            steps {
                sh """
                  kubectl get namespace ${K8S_NAMESPACE} \
                  || kubectl create namespace ${K8S_NAMESPACE}
                """
            }
        }

        stage('Deploy MongoDB') {
            steps {
                sh """
                  kubectl apply -f mongo.yaml
                """
            }
        }

        stage('Verify MongoDB Deployment') {
            steps {
                sh """
                  kubectl rollout status deployment/${MONGO_APP} \
                  -n ${K8S_NAMESPACE}
                """
            }
        }

        stage('Verify MongoDB Service') {
            steps {
                sh """
                  kubectl get svc ${MONGO_APP} -n ${K8S_NAMESPACE}
                """
            }
        }
    }

    post {
        success {
            echo "✅ MongoDB deployed successfully in ${K8S_NAMESPACE} namespace"
        }
        failure {
            echo "❌ MongoDB deployment failed"
        }
    }
}
