pipeline {
    agent any 
    tools {
        jdk 'jdk'
        nodejs 'nodejs'
    }
   environment {
        DOCKER_HUB_LOGIN = credentials('dockerHub')
        IMAGE='app-frontend'
        REGISTRY='crist'
    }

    stages {

        stage('Cleaning Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Checkout from Git') {
            steps {
                git credentialsId: 'GITHUB', url: 'https://github.com/cristhiancaldas/devops-frontend.git'
            }
        }
        
        stage('OWASP Dependency-Check Scan') {
            steps {             
                    dependencyCheck additionalArguments: '--scan ./ --disableYarnAudit --disableNodeAudit', odcInstallation: 'DP-Check'
                    dependencyCheckPublisher pattern: '**/dependency-check-report.xml'              
            }
        }
        
        stage('Trivy File Scan') {
            steps {
                    sh 'trivy fs . > trivyfs.txt'          
            }
        }
        
        stage('build') {
            steps {
               sh 'docker build --platform linux/amd64 -t $IMAGE:${BUILD_NUMBER} .'
            }
        }
        
        stage('deploy to hub') {
            steps {
               sh '''
               docker login --username=$DOCKER_HUB_LOGIN_USR --password=$DOCKER_HUB_LOGIN_PSW
               docker tag $IMAGE:${BUILD_NUMBER} $REGISTRY/$IMAGE:${BUILD_NUMBER}
               docker push $REGISTRY/$IMAGE:${BUILD_NUMBER}
               '''
            }
        }
  
    }
}