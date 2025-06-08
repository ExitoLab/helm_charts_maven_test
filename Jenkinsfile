pipeline {
    agent any

    environment {
        OPENSHIFT_URL = 'https://api.c5181g0i.centralus.aroapp.io:6443'  // Change to your actual OpenShift API endpoint
        RELEASE_NAME = 'maven-test'
        CHART_PATH = '.'
        NAMESPACE = 'jenkins-agents'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    url: 'https://github.com/ExitoLab/helm_charts_maven_test.git'
            }
        }   

        stage('Login to OpenShift') {
            steps {
                withCredentials([string(credentialsId: 'openshift-token', variable: 'OC_TOKEN')]) {
                    sh """
                        echo 'Logging into OpenShift...'
                        oc login ${env.OPENSHIFT_URL} --token=$OC_TOKEN --insecure-skip-tls-verify
                    """
                }
            }
        }

        stage('Lint Helm Chart') {
            steps {
                sh "helm lint ."
            }
        }

        stage('Deploy Helm Chart') {
            steps {
                sh """
                    helm upgrade --install $RELEASE_NAME $CHART_PATH \
                      --namespace $NAMESPACE \
                      --values values.yaml 
                """
            }
        }

        stage('Verify Rollout') {
            steps {
                sh "oc rollout status deployment/$RELEASE_NAME -n $NAMESPACE"
            }
        }
    }
}
