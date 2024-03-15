pipeline {
    agent any

        environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Clone Repository'){
            steps{
                git branch: 'main',credentialsId:'git-account',url: 'https://github.com/Sravani-738/hiring-app.git'}
        }
        stage('Build Docker Image'){
            steps{
                script{
                    sh "docker build . -t sravani738/hiring-app:$BUILD_NUMBER"
                }
            }
        }
                    
            
        stage('Docker Push') {
            steps {
                withCredentials([string(credentialsId: 'docker-hub', variable: 'hubPwd')]) {
                    sh "docker login -u  -p ${Sravs@738}"
                    sh "docker push sravani738/hiring-app:$BUILD_NUMBER"
                }
            }
        }
        stage('Checkout K8S manifest SCM'){
            steps {
              git branch: 'main', url: 'https://github.com/betawins/Hiring-app-argocd.git'
            }
        } 
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                   withCredentials([usernamePassword(credentialsId: 'Github_server', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) { 
                        sh '''
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        sed -i "s/5/${BUILD_NUMBER}/g" /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        cat /var/lib/jenkins/workspace/$JOB_NAME/dev/deployment.yaml
                        git add .
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://$GIT_USERNAME:$GIT_PASSWORD@github.com/betawins/Hiring-app-argocd.git main
                        '''                        
                      }
                  }
            }   
        }
            }
} 
