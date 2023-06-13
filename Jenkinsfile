#!/usr/bin/env groovy
pipeline {
    agent {
        label 'slave'
    }
    
    stages {
        stage('Build and Push Docker Image') {
            steps {
                echo "Building docker image..."
                script {
                    withCredentials ([usernamePassword(credentialsId:'dockerhub',usernameVariable: 'USER',passwordVariable:'PASS')]) {
                        sh '''
                            docker build -t minasaiedbasta/web-app-jenkins:v${BUILD_NUMBER} .
                            echo $PASS | docker login -u $USER --password-stdin
                            docker push minasaiedbasta/web-app-jenkins:v${BUILD_NUMBER}
                            echo ${BUILD_NUMBER} > ../build.txt
                        '''
                    }
                }
            }
        }
        
        stage('Deploy Docker Image') {
            steps {
                echo 'Deploy the released Docker image'
                script {
                    sh '''
                        export BUILD_NUMBER=$(cat ../build.txt)
                        mv deployment/deploy.yaml deployment/deploy.yaml.tmp
                        cat deployment/deploy.yaml.tmp | envsubst > deployment/deploy.yaml
                        rm -f deployment/deploy.yaml.tmp
                        kubectl apply -f deployment
                    '''
                }
            }
        }
    }
}