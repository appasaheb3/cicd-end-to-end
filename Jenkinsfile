pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
                git credentialsId: '935c521a-1de8-44d7-8873-fa9ca9979394', 
                url: 'https://github.com/appasaheb3/cicd-end-to-end',
                branch: 'main'
           }
        }

        stage('Build Docker'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t appasaheb3/cicd-e2e:${BUILD_NUMBER} .
                    '''
                }
            }
        }

        stage('Push the artifacts'){
           steps{
               withCredentials([usernamePassword(credentialsId: 'eac9cdca-a1a0-4d58-86f2-4928f02a64e9', passwordVariable: 'DOCKER_HUB_PASSWORD', usernameVariable: 'DOCKER_HUB_USERNAME')]) {
                    sh '''
                    echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin' 
                    echo 'Push to Repo'
                    docker push appasaheb3/cicd-e2e:${BUILD_NUMBER}
                    '''
                }
            }
        }
        
        stage('Checkout K8S manifest SCM'){
            steps {
                git credentialsId: '935c521a-1de8-44d7-8873-fa9ca9979394', 
                url: 'https://github.com/appasaheb3/cicd-demo-manifests-repo.git',
                branch: 'main'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([usernamePassword(credentialsId: '935c521a-1de8-44d7-8873-fa9ca9979394', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                        sh '''
                        cat deploy.yaml
                        sed -i '' "s/32/${BUILD_NUMBER}/g" deploy.yaml
                        cat deploy.yaml
                        git add deploy.yaml
                        git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                        git remote -v
                        git push https://github.com/appasaheb3/cicd-demo-manifests-repo.git HEAD:main
                        '''                        
                    }
                }
            }
        }
    }
}
