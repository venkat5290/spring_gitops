pipeline{
    agent any
    environment {
            imageName = "venkat7010/springboot-devsecops"
            dockerImage = ''
            dockerImageTag = "${imageName}:${BUILD_NUMBER}"
        }
    stages{
        stage('Checkout App'){
            steps{
                echo "before checkin out"
                git branch: 'main', url: 'https://github.com/venkat5290/spring_gitops.git'
                echo "After checking out"
            }
        }
        stage('Deploy to K8s') {
            steps{
                script {
                    sh "kubectl --kubeconfig=~/.kube/config apply -f gitops_deployment.yml"
                }
            }
        } 
    }
}