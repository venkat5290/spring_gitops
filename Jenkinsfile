pipeline{
    agent any
     environment {
        APP_NAME = "springboot-devsecops"
        IMAGE_TAG = "${params.IMAGE_TAG}"
    }
    stages{
        stage('print Image Tag') {
            steps {
                sh "echo ${IMAGE_TAG}"
            }
        }
        stage('Cleanup Workspace'){
            steps {
                script {
                    cleanWs()
                }
            }
        }
        stage('Checkout App'){
            steps{
                echo "before checkin out"
                git branch: 'main', url: 'https://github.com/venkat5290/spring_gitops.git'
                echo "After checking out"
            }
        }
       stage('setup kubeconfig') {
          steps {
            withCredentials([file(credentialsId: 'gkecluster', variable: 'cd_config')]) {
                sh "sudo cp \${cd_config} ${WORKSPACE}/cd_config"
                }
           }
       }
       stage('Updating Kubernetes deployment file'){
            steps {
                sh "cat gitops_deployment.yml"
                sh "sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' gitops_deployment.yml"
                sh "cat gitops_deployment.yml"
            }
        }
        stage('deploy to gke cluster') {
          steps {
             sh '''
                sudo kubectl --kubeconfig ${WORKSPACE}/cd_config config set-context --current --user=gitops-sa
                sudo kubectl apply -f gitops_deployment.yml --kubeconfig ${WORKSPACE}/cd_config 
                '''
            }
        }
        stage('remove kubeconfig file') {
            steps {
                sh "sudo rm -rf ${WORKSPACE}/cd_config"
            }
        }
    }
}