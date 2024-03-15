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
                    withCredentials([usernamePassword(credentialsId: 'git', passwordVariable: 'Sravs@738', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        sed -i "s/5/${BUILD_NUMBER}/g" /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        git add .
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://$GIT_USERNAME:$GIT_PASSWORD@github.com/Sravani-738/Hiring-app-argocd.git main
                        '''
                    }
                }
            }
        }
    }
}
