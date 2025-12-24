pipeline {
    agent any
    
    stages {
        stage('Cleanup workspace') {
            steps {
                cleanWs()
            }
        }
        
        stage('Checkout from SCM') {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/ekelejames/update-manifest.git'
            }
        }
        
        stage('Update GIT') {
            steps {
                script {
                    catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                        withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'GIT_PASSWORD', usernameVariable: 'GIT_USERNAME')]) {
                            sh "git config user.email ekelejames16@gmail.com"
                            sh "git config user.name ekelejay"
                            sh "cat deployment.yaml"
                            sh "sed -i 's+ekelejay/test-app:[^[:space:]]*+ekelejay/test-app:${DOCKERTAG}+g' deployment.yaml"
                            sh "cat deployment.yaml"
                            sh "git add ."
                            sh "git commit -m 'Done by Jenkins Job changemanifest: ${env.BUILD_NUMBER}'"
                            sh "git push https://${GIT_USERNAME}:${GIT_PASSWORD}@github.com/${GIT_USERNAME}/update-manifest.git HEAD:main"
                        }
                    }
                }
            }
        }
    }
    
    post {
        success {
            echo "Successfully updated manifest with image tag: ${DOCKERTAG}"
        }
        failure {
            echo "Manifest update failed!"
        }
    }
}