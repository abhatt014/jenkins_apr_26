pipeline {
 agent any

 environment {
 TAG = "${env.BUILD_NUMBER}"
 }

 stages {
    stage('clone') {
        steps {
            git branch: 'main',
            credentialsId: 'github-pat',
            poll: false,
            url: 'https://github.com/abhatt014/jenkins_apr_26.git'
        }
    }
    stage('remove_images'){
        steps{
             sh 'docker rmi amitfreeze/apr_jen_auth:$((TAG-1)) || true'
             sh 'docker rmi amitfreeze/apr_jen_frontend:$((TAG-1)) || true'
             sh 'docker rmi amitfreeze/apr_jen_order:$((TAG-1)) || true'
             sh 'docker rmi amitfreeze/apr_jen_product:$((TAG-1)) || true'
         }
    }
    stage('build') {
        steps{
             sh 'docker build -t "amitfreeze/apr_jen_auth:$TAG" -t "amitfreeze/apr_jen_auth:latest" ./auth-service'
             sh 'docker build -t "amitfreeze/apr_jen_frontend:$TAG" -t "amitfreeze/apr_jen_frontend:latest" ./frontend-service'
             sh 'docker build -t "amitfreeze/apr_jen_order:$TAG" -t "amitfreeze/apr_jen_order:latest" ./order-service'
             sh 'docker build -t "amitfreeze/apr_jen_product:$TAG" -t "amitfreeze/apr_jen_product:latest" ./product-service'
        }

     }
    stage('push') {
        steps {
             withCredentials([usernamePassword(credentialsId: 'dockerhub',
             usernameVariable: 'DOCKERHUB_USER',
             passwordVariable: 'DOCKERHUB_PWD')]) {
                 sh 'echo "$DOCKERHUB_PWD" | docker login -u $DOCKERHUB_USER --password-stdin' 
                 sh 'docker push "amitfreeze/apr_jen_auth:$TAG"' 
                 sh 'docker push "amitfreeze/apr_jen_auth:latest"' 
                 sh 'docker push "amitfreeze/apr_jen_frontend:$TAG"' 
                 sh 'docker push "amitfreeze/apr_jen_frontend:latest"'
                 sh 'docker push "amitfreeze/apr_jen_order:$TAG"'
                 sh 'docker push "amitfreeze/apr_jen_order:latest"'	
                 sh 'docker push "amitfreeze/apr_jen_product:$TAG"'
                 sh 'docker push "amitfreeze/apr_jen_product:latest"'
            }
        }
    }
    stage('deploy') {
        steps {
         sh 'docker compose down'
         sh 'docker compose up -d'
        }
    }
 stage('test') {
    steps {
        sh 'echo "Hit http://192.168.81.171:5001 to see the app."'
    }
 }
 }
}
