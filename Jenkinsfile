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
                  kubectl rollout status deployment/${MONGO_APP} -n ${K8S_NAMESPACE}
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
            emailext(
                subject: "✅ SUCCESS: ${JOB_NAME} #${BUILD_NUMBER}",
                mimeType: 'text/html',
                body: """
                    <h2 style="color:green;">MongoDB Deployment Successful 🎉</h2>
                    <p><b>Namespace:</b> ${K8S_NAMESPACE}</p>
                    <p><b>Application:</b> ${MONGO_APP}</p>
                    <p><b>Job:</b> ${JOB_NAME}</p>
                    <p><b>Build Number:</b> ${BUILD_NUMBER}</p>
                    <p>
                        <b>Build URL:</b>
                        <a href="${BUILD_URL}">${BUILD_URL}</a>
                    </p>
                """,
                to: "erdigvijaypatil01@gmail.com"
            )
        }

        failure {
            emailext(
                subject: "❌ FAILURE: ${JOB_NAME} #${BUILD_NUMBER}",
                mimeType: 'text/html',
                body: """
                    <h2 style="color:red;">MongoDB Deployment Failed ❌</h2>
                    <p><b>Namespace:</b> ${K8S_NAMESPACE}</p>
                    <p><b>Application:</b> ${MONGO_APP}</p>
                    <p><b>Job:</b> ${JOB_NAME}</p>
                    <p><b>Build Number:</b> ${BUILD_NUMBER}</p>
                    <p>
                        <b>Build URL:</b>
                        <a href="${BUILD_URL}">${BUILD_URL}</a>
                    </p>
                    <p>Please check the Jenkins logs for more details.</p>
                """,
                to: "erdigvijaypatil01@gmail.com"
            )
        }
    }
}
