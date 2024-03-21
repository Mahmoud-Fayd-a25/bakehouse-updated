pipeline {
    agent {
        label 'mahmoudfayd25'
    }

    stages {
        stage('build') {
            when { 
                anyOf { branch 'dev' ;  branch 'test' ;  branch 'release' }
            }
            steps {
                echo 'build'
                withCredentials([usernamePassword(credentialsId: 'dockerhub-mahmoudfayd25', usernameVariable: 'DOCKERHUB_USERNAME', passwordVariable: 'DOCKERHUB_PASSWORD')]) {
                    sh '''
                    docker build -t mahmoudfayd25/bakehouse:v${BUILD_NUMBER}-${BRANCH_NAME} .
                    docker login -u $DOCKERHUB_USERNAME -p $DOCKERHUB_PASSWORD
                    docker push mahmoudfayd25/bakehouse:v${BUILD_NUMBER}-${BRANCH_NAME}
                    '''
                }
            }
        }       
       stage('deploy') {
           when { 
                anyOf { branch 'dev' ;  branch 'test' ;  branch 'release' }
            }
            steps {
                echo 'deploy'
                withCredentials([file(credentialsId: 'kubeconfig-cred', variable: 'KUBECONFIG_MAHMOUD')]) {
                    sh '''
                     cat Deployment/deploy.yaml | envsubst > Deployment/deploy.yaml.temp
                     mv Deployment/deploy.yaml.temp Deployment/deploy.yaml
                     kubectl apply -f Deployment --kubeconfig $KUBECONFIG_MAHMOUD  -n ${BRANCH_NAME}  
   
                    '''
                }
                }
            }    
        
    }
}    
