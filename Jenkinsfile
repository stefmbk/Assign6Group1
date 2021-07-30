def commit_id
pipeline {
    agent any
    stages {
        stage('preparation') {
            steps {
                checkout scm
                sh "git rev-parse --short HEAD > .git/commit-id"
                script {
                    commit_id = readFile('.git/commit-id').trim()
                }
            }
        }
        stage ('build') {
            steps {
                echo 'Build maven project'
                sh "mvn install"
                echo 'Maven project has been built successfully'
            }
        }

        stage ("image build") {
            steps {
                echo 'Image building'
                sh "docker build -t 2alinfo7/position-simulator:${commit_id} ."
                echo 'Image has been built successfully'
            }
        }
        stage ("image push") {
            steps {
                echo 'image push to docker hub'
                sh "docker push 2alinfo7/position-simulator:${commit_id} "
                echo 'docker image has been pushed successffully'
            }
        }
        stage('deploy') {
            steps {
                echo 'Image deployment'
                sh "sed -i -r 's|richardchesterwood/k8s-fleetman-position-simulator:release2|2alinfo7/position-simulator:${commit_id}|' workloads.yaml"
                sh 'kubectl apply -f workloads.yaml --namespace assign6group1'
                echo 'Image has been deployed successfully'
            }
        }
    }
}
