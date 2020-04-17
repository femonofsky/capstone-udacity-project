pipeline {
    environment {
        dockerhubCredentials = 'dockerhub'
    }
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
                                sh 'hadolint ./Dockerfile | tee -a hadolint_lint.txt'
                                sh '''
                                    lintErrors=$(stat --printf="%s"  hadolint_lint.txt)
                                    if [ "$lintErrors" -gt "0" ]; then
                                        echo "Errors have been found, please see below"
                                        cat hadolint_lint.txt
                                    exit 1
                                    else
                                        echo "There are no erros found on Dockerfile!!"
                                    fi
                                '''
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

        stage('Deploying to EKS') {
            steps {
                dir('k8s') {
                    withAWS(credentials: 'mini', region: 'us-west-2') {
                            sh "aws eks --region us-west-2 update-kubeconfig --name eks-stack-EKS-Cluster" 
                            sh "kubectl config use-context arn:aws:eks:us-west-2:472858849231:cluster/eks-stack-EKS-Cluster"
                            sh "kubectl describe configmap -n kube-system aws-auth"
                            sh "kubectl apply -f aws-auth-cm.yaml"
                            sh "aws sts get-caller-identity"
                            sh "kubectl get svc"
                            sh "kubectl apply -f service.yaml"
                            sh "kubectl apply -f deploy.yaml"
                        }
                    }
            }
        }
    }
}