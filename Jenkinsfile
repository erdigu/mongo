pipeline {
    agent any

    environment {
        K8S_NAMESPACE = "automotive"
        MONGO_APP     = "mongo"
        AWS_DEFAULT_REGION = "us-east-1"
    }

    stages {

        stage('Verify Namespace') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds-4eks', // Your Jenkins AWS credentials
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh """
                      aws eks update-kubeconfig --name automotive-cluster --region $AWS_DEFAULT_REGION
                      kubectl get namespace ${K8S_NAMESPACE} || kubectl create namespace ${K8S_NAMESPACE}
                    """
                }
            }
        }

        stage('Deploy MongoDB') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds-4eks',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh """
                      kubectl apply -f mongo.yaml
                    """
                }
            }
        }

        stage('Verify MongoDB Deployment') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds-4eks',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh """
                      kubectl rollout status deployment/${MONGO_APP} -n ${K8S_NAMESPACE}
                    """
                }
            }
        }

        stage('Verify MongoDB Service') {
            steps {
                withCredentials([[
                    $class: 'AmazonWebServicesCredentialsBinding',
                    credentialsId: 'aws-creds-4eks',
                    accessKeyVariable: 'AWS_ACCESS_KEY_ID',
                    secretKeyVariable: 'AWS_SECRET_ACCESS_KEY'
                ]]) {
                    sh """
                      kubectl get svc ${MONGO_APP} -n ${K8S_NAMESPACE}
                    """
                }
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
