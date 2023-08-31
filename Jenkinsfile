pipeline {
    agent {
        node {
            label 'maven'
        }
    }
    stages {
        stage('Tests') {
            steps {
                sh './mvnw clean test'
            }
        }
        stage('Package') {
            steps {
                sh '''
                    ./mvnw package -DskipTests \
                    -Dquarkus.package.type=uber-jar
                '''
                archiveArtifacts 'target/*.jar'
            }
        }
        stage('Build Image') {
            environment { QUAY = credentials('jnino') } 1
            steps {
                sh '''
                    ./mvnw quarkus:add-extension \
                    -Dextensions="kubernetes,container-image-jib" 2
                '''
                sh '''
                    ./mvnw package -DskipTests \
                    -Dquarkus.jib.base-jvm-image=quay.io/redhattraining/do400-java-alpine-openjdk11-jre:latest \
                    -Dquarkus.container-image.build=true \ 3
                    -Dquarkus.container-image.registry=quay.io \ 4
                    -Dquarkus.container-image.group=jnino \ 5
                    -Dquarkus.container-image.name=do400-deploying-environments \ 6
                    -Dquarkus.container-image.username=jnino \ 7
                    -Dquarkus.container-image.password="Jonh24680" \ 8
                    -Dquarkus.container-image.push=true 9
                '''
            }
        }
    }
}
