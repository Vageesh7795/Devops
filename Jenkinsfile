pipeline {
    agent any

    environment {
        PROJECT = "cicd-demo"
        APP_NAME = "sample-web"
        REGISTRY = "image-registry.openshift-image-registry.svc:5000"
        IMAGE = "${REGISTRY}/${PROJECT}/${APP_NAME}:latest"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Image') {
            steps {
                sh """
                    podman build -t ${IMAGE} .
                """
            }
        }

        stage('Login Registry') {
            steps {
                sh """
                    oc registry login
                """
            }
        }

        stage('Push Image') {
            steps {
                sh """
                    podman push ${IMAGE}
                """
            }
        }

        stage('Deploy') {
            steps {
                sh """
                    oc project ${PROJECT}
                    oc apply -f k8s/deployment.yaml
                    oc apply -f k8s/service.yaml
                    oc apply -f k8s/route.yaml
                """
            }
        }

        stage('Rollout') {
            steps {
                sh """
                    oc rollout status deployment/${APP_NAME} --timeout=180s
                """
            }
        }

        stage('Get URL') {
            steps {
                sh """
                    oc get route ${APP_NAME}
                """
            }
        }
    }
}
