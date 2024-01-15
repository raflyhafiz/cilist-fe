pipeline {  
  agent any 
  options {
    buildDiscarder(logRotator(numToKeepStr: '5'))   
  }
  stages {
    stage('Build Image Frontend') {
        steps {
            script{
                if (env.BRANCH_NAME == 'staging') {
                    dir('frontend'){
                        sh 'docker build -t raflyhafiz/cilist-fe:0.0.$BUILD_NUMBER-staging .'
                    }
                }
                else if (env.BRANCH_NAME == 'master') {
                    dir('frontend'){
                         sh 'docker build -t raflyhafiz/cilist-fe:0.0.$BUILD_NUMBER-master .' 
                    }
                }
                else {
                     sh 'echo Nothing to Build'
                }
            }
        }
    }
    stage('Push to Registry Frontend') {
        steps {
            script {
             if (env.BRANCH_NAME == 'staging') {
            sh 'docker push raflyhafiz/cilist-fe:0.0.$BUILD_NUMBER-staging'
                }
                else if (env.BRANCH_NAME == 'master') {
            sh 'docker push raflyhafiz/cilist-fe:0.0.$BUILD_NUMBER-master' 
                }
                else {
                    sh 'echo Nothing to Push'
                }
        }
      }
    } 
    stage('Deploy to Kubernetes') {
        steps {
            script {
                if (env.BRANCH_NAME == 'staging') {
                    kubeconfig(credentialsId: 'configk8', serverUrl: '') {
                        sh 'cat frontend-stag/fe-stag.yaml | sed "s/{{NEW_TAG}}/0.0.$BUILD_NUMBER-staging/g" | kubectl apply -f -'
                    }
                }
                else if (env.BRANCH_NAME == 'master') {
                    kubeconfig(credentialsId: 'configk8', serverUrl: '') {
                        sh 'cat frontend-prod/fe-prod.yaml | sed "s/{{NEW_TAG}}/0.0.$BUILD_NUMBER-master/g" | kubectl apply -f -'
                    }
                }
                else {
                    echo "Tidak ada yg dideploy"
                }
            }
        }
    } 
}
     post {
        success {
            slackSend channel: '#jenkinsnotif',
                      color: 'good',
                      message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
        }    
        failure {
            slackSend channel: '#jenkinsnotif',
                      color: 'danger',
                      message: "*${currentBuild.currentResult}:* Job ${env.JOB_NAME} build ${env.BUILD_NUMBER}\n More info at: ${env.BUILD_URL}"
        }
    }   
}


