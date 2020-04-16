pipeline {
    // environment {
    //     dockerhubCredentials = 'dockerhubCredentials'
    // }
    agent any
    stages {
        stage('Lint HTML') {
            steps {
                dir('app') {
                    sh 'tidy -q -e *.html'
                }
            }
        }
        stage('Lint Dockerfile') {
            steps {
                script {
                    dir('app') {
                            docker.image('hadolint/hadolint:latest-debian').inside() {
                                
                            }
                        }
                }
            }
        }

        stage('Build & Push to dockerhub') {
            steps {
                script {
                    dir('app') {
                        dockerImage = docker.build("nofsky/udacity-static-capstone:${env.BUILD_ID}")
                        docker.withRegistry('', dockerhubCredentials) {
                            dockerImage.push('latest')
                        }
                    }
                }
            }
        }
        // stage('Deploying to EKS') {
        //     steps {
        //         dir('k8s') {
        //             withAWS(credentials: 'aws-credentials', region: 'eu-west-2') {
        //                     sh "aws eks --region eu-west-2 update-kubeconfig --name capstone"
        //                     sh 'kubectl apply -f capstone-k8s.yaml'
        //                 }
        //             }
        //     }
        // }
    }
}