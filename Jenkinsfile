pipeline {
    agent any

    stages {
        stage('Validate Kubernetes') {
            steps {
                bat 'kubectl version --client'
            }
        }

        stage('Deploy Initial Application') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'kubeconfig-credentials',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    bat 'kubectl apply -f deployment.yaml'
                    bat 'kubectl rollout status deployment/set13-deployment --timeout=180s'
                }
            }
        }

        stage('Check Initial Deployment') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'kubeconfig-credentials',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    bat 'kubectl get deployment set13-deployment'
                    bat 'kubectl rollout history deployment/set13-deployment'
                }
            }
        }

        stage('Perform Rolling Update') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'kubeconfig-credentials',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    bat 'kubectl set image deployment/set13-deployment nginx=nginx:1.27'
                    bat 'kubectl rollout status deployment/set13-deployment --timeout=180s'
                }
            }
        }

        stage('Verify Rollout') {
            steps {
                withCredentials([
                    file(
                        credentialsId: 'kubeconfig-credentials',
                        variable: 'KUBECONFIG'
                    )
                ]) {
                    bat 'kubectl get deployment set13-deployment'
                    bat 'kubectl get pods -l app=set13-app'
                    bat 'kubectl get service set13-service'
                    bat 'kubectl rollout history deployment/set13-deployment'
                    bat 'kubectl get deployment set13-deployment -o jsonpath={.spec.template.spec.containers[0].image}'
                }
            }
        }
    }
}