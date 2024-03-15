pipeline {
    agent any
    environment {
        IMAGE_TAG = "$BUILD_NUMBER"
    }
    stages {
        stage('Docker Build') {
            steps {
                sh "docker build . -t sravani738/hiring-app:$BUILD_NUMBER"
            }
        }
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub', variable: 'hubPwd')]) {
                    sh "docker login -u sravani738 -p ${hubPwd}"
                    sh "docker push sravani738/hiring-app:$BUILD_NUMBER"
                }
            }
        }
        stage('Checkout K8S manifest SCM'){
            steps {
                git branch: 'main', url: 'https://github.com/Sravani-738/Hiring-app-argocd.git'
            }
        }
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([string(credentialsId: 'githubtoken', variable: 'GIT_TOKEN')]) {
                        sh '''
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        sed -i "s/19/${BUILD_NUMBER}/g" /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        git add .
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://$GIT_TOKEN@github.com/Sravani-738/Hiring-app-argocd.git main
                        '''
                    }
                }
            }
        }
    }
}
