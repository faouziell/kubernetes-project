def commit_id
pipeline{
    agent any


    stages{
        stage('preparation') {
            steps{
                checkout scm
                sh "git rev-parse --short HEAD > .git/commit-id"
                script {
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }

        stage ('Image Build') {
            steps {
                echo 'Building....'
                sh 'scp -r -i $(minikube ssh-key) ./*  docker@$(minikube ip):-/'
                sh "minikube ssh 'docker build -t webapp:${commit_id} ./'"
                echo 'build complete'
            }
        }

        stage('Deploy'){
            steps {
                echo 'Deploying to kubernets'
                sh "sed -i -r 's|richardchesterwood/k8s-fleetman-angular:release2|webapp:${commit_id}' ./manifests/deployments.yaml"
                sh 'kubectl get all'
                sh ' kubectl apply -f -/manifests/ '
                sh ' kubectl get all'
                echo 'deployment complete'
            }
                
            }
        }
    }
}
