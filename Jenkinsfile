pipeline{
    agent any
    stages{
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
        stage('deploy to gke cluster') {
          steps {
             sh '''
                sudo kubectl --kubeconfig ${WORKSPACE}/cd_config config set-context --current --user=cd-sa
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