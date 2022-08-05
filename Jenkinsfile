pipeline {
    agent none
    environment {
        DOCKER_ID = "philjim"
        DOCKER_PASSWORD = credentials('dockerlogin')
    }
    stages{
        stage('Worker Build'){
            agent{
                docker{
                    image 'maven:3.8.6-openjdk-18-slim'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            when{
                changeset "**/worker/**"
            }
            steps{
                echo "Compile the worker app"
                dir ('worker'){
                    sh 'mvn compile'
                }
            }
        }
        stage('Worker Test'){
            agent{
                docker{
                    image 'maven:3.8.6-openjdk-18-slim'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            when{
                changeset "**/worker/**"
            }
            steps{
                echo "Run Unit Tests on Worker app"
                dir ('worker'){
                    sh 'mvn clean test'
                }
            }
        }
        stage('Worker Package'){
            agent{
                docker{
                    image 'maven:3.8.6-openjdk-18-slim'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            when{
                branch 'master'
                changeset "**/worker/**"
            }
            steps{
                echo "package the worker app"
                dir ('worker'){
                    sh 'mvn package -DskipTests'
                    archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
                }
            }
        }
        stage('Worker Docker-Package'){
            agent any
            when{
                branch 'master'
                changeset "**/worker/**"
            }
            steps{
                echo "package the worker app with docker"
                script{
                    docker.withRegistry('https://index.docker.io/v1'){
                       sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_ID --password-stdin'
                       def workerImage = docker.build("philjim/worker:${env.BUILD_ID}","./worker")
                       workerImage.push()
                       workerImage.push("latest")
                    }
                }
            }
        }

        stage('Vote Test'){
        agent{
                docker{
                    image 'python:2.7.16-slim'
                    args '--user root'
                }
            }
            when{
                changeset "**/vote/**"
            }
            steps{
                echo "Compile and test the vote app"
                dir ('vote'){
                    sh 'pip install -r requirements.txt'
                    sh 'nosetests -v'
                }
            }
        }
        stage('Vote Docker-Package'){
            agent any
            when{
                branch 'master'
                changeset "**/vote/**"
            }
            steps{
                echo "package the result vote with docker"
                script{
                    docker.withRegistry('https://index.docker.io/v1'){
                        sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_ID --password-stdin'
                        def voteImage = docker.build("philjim/vote:${env.BUILD_ID}","./vote")
                        voteImage.push()
                        voteImage.push("latest")
                    }
                }
            }
        }

        stage('Result Build'){
            agent{
                docker{
                    image 'node:8.9-alpine'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            when{
                changeset "**/result/**"
            }
            steps{
                echo "Compile the result app"
                dir ('result'){
                    sh 'npm install'
                }
            }
        }
        stage('Result Test'){
            agent{
                docker{
                    image 'node:8.9-alpine'
                    args '-v $HOME/.m2:/root/.m2'
                }
            }
            when{
                changeset "**/result/**"
            }
            steps{
                echo "package the result app"
                dir ('result'){
                    sh 'npm install'
                    sh 'npm test'

                }
            }
        }
        stage('Result Docker-Package'){
            agent any
            when{
                branch 'master'
                changeset "**/result/**"
            }
            steps{
                echo "package the result app with docker"
                script{
                    docker.withRegistry('https://index.docker.io/v1'){
                       sh 'echo $DOCKER_PASSWORD | docker login -u $DOCKER_ID --password-stdin'
                       def resultImage = docker.build("philjim/result:${env.BUILD_ID}","./result")
                       resultImage.push()
                       resultImage.push("latest")
                    }
                }
            }
        }

    }

    post{
        always{
            echo "build pipeline completed"
        }
    }
}