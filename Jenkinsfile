pipeline {
    agent any

    environment {
        SERVICE_NAME = "testml"
        DOCKERHUB_USERNAME = "frankisinfotech"
        REPOSITORY_TAG = "${DOCKERHUB_USERNAME}/${SERVICE_NAME}:${BUILD_ID}"
        DOCKER_PASSWORD = credentials('DOCKER_PASSWORD')
    }

    stages {
        
        stage('Install Dependencies') {
            steps {
                bat 'pip install scikit-learn kfp numpy pandas boto3'
            }
        }
        stage('Run Script') {
            steps {
                bat 'python model.py'
            }
        }
        stage('Build Image') {
            steps {
                    bat '''
                        echo "${DOCKER_PASSWORD}" | docker login -u frankisinfotech --password-stdin
                        docker build -t ${REPOSITORY_TAG} .
                        docker push ${REPOSITORY_TAG}
                    '''
        }
        }

        stage("Install kubectl"){
            steps {
                bat """
                    curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
                    chmod +x ./kubectl
                    ./kubectl version --client
                """
            }
        }
        

        stage ('Deploy to Cluster') {
            steps {
                //sh "aws eks update-kubeconfig --region eu-west-1 --name switch-arca-qa-cluster"
                bat " envsubst < ${WORKSPACE}/deploy.yaml | ./kubectl apply -f - "
            }
        }

    }
}
          
      



