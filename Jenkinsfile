pipeline {
    agent any

    environment {
        SERVICE_NAME = "testml"
        DOCKERHUB_USERNAME = "frankisinfotech"
        REPOSITORY_TAG = "${DOCKERHUB_USERNAME}/${SERVICE_NAME}:${BUILD_ID}"
    }

    stages {
        stage('Install Dependencies') {
            steps {
                sh 'pip install scikit-learn'
            }
        }
        stage('Run Script') {
            steps {
                sh 'python3 model.py'
            }
        }
        stage('Build Image') {
            steps {
                 withDockerRegistry([credentialsId: 'DOCKERHUB_USERNAME', url: ""]) {
                    sh '''
                        docker build -t ${REPOSITORY_TAG} .
                        docker push ${REPOSITORY_TAG}
                    '''
            }
        }

        stage("Install kubectl"){
            steps {
                sh """
                    curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
                    chmod +x ./kubectl
                    ./kubectl version --client
                """
            } 
        

        stage ('Deploy to Cluster') {
            steps {
                //sh "aws eks update-kubeconfig --region eu-west-1 --name switch-arca-qa-cluster"
                sh " envsubst < ${WORKSPACE}/deploy.yaml | ./kubectl apply -f - "
            }
        }

    }
}
          
      



