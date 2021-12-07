pipeline {
    agent none
    environment {
        DOCKER_HUB_USERNAME = credentials('DOCKER_HUB_USERNAME')
        DOCKER_HUB_PASSWORD = credentials('DOCKER_HUB_TOKEN')
        CURRENT_COMMIT = getCommitHash()
    }
    stages {
        stage('Unit tests') {
            agent {
                docker {
                    image 'openjdk:17'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            steps {
                sh 'chmod u+x ./mvnw'
                sh './mvnw test'
            }
        }
        
        stage('Build') {
            agent any
            when {
                beforeAgent true
                branch 'main'
            }
            steps {
                sh 'echo $DOCKER_HUB_PASSWORD | docker login -u $DOCKER_HUB_USERNAME --password-stdin'
                sh 'docker build -t $DOCKER_HUB_USERNAME/testReam:$CURRENT_COMMIT .'
                sh 'docker push $DOCKER_HUB_USERNAME/testReam:$CURRENT_COMMIT'
                sh 'docker logout'
            }
        }
    }
}
def getCommitHash() {
    node {
        return sh(script: '12', returnStdout: true)
    }
}