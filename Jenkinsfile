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
       stage('Updating Kubernetes deployment file'){
            steps {
                sh "cat gitops_deployment.yml"
                sh "sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g' gitops_deployment.yml"
                sh "cat gitops_deployment.yml"
            }
        }
       stage('Pushing changes to git'){
            steps{
                script{
                    sh """
                    git config --global user.name 'venkat5290'
                    git config --global user.email 'injarapu.venkat@gmail.com' 
                    git add gitops_deployment.yml
                    git commit -m 'Image updated for deployment' """
                    withCredentials([usernamePassword(credentialsId: 'github', usernameVariable: 'username', passwordVariable: 'password')]) {
                        def encodedPassword = URLEncoder.encode("$PASSWORD",'UTF-8')
                        sh "git push https://$username:$encodedPassword@github.com/venkat5290/spring_gitops.git main"
                    }
                }
            }
       }
    }
}