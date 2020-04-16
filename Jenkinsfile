pipeline {
    // environment {
    //     dockerhubCredentials = 'dockerhubCredentials'
    // }
    agent any
    stages {
        stage('Lint HTML') {
            steps {
                sh 'tidy -q -e *.html'
            }
        }
        stage('Lint Dockerfile') {
            steps {
                script {
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

        // stage('Build & Push to dockerhub') {
        //     steps {
        //         script {
        //             dockerImage = docker.build("nofsky/udacity-static-capstone:${env.BUILD_ID}")
        //             docker.withRegistry('', dockerhubCredentials) {
        //                 dockerImage.push('latest')
        //             }
        //         }
        //     }
        // }
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