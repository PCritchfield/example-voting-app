pipeline {
    agent any
    tools{
        maven 'maven3.8.6'
    }
    stages{
        stage('build'){
            echo "Compile the worker app"
            dir ('worker'){
                sh 'mvn compile'
            }
        }
        stage('test'){
            echo "Run Unit Tests on Worker app"
            dir ('worker'){
                sh 'mvn compile'
            }
        }
        stage('package'){
            echo " package the worker app"
            dir ('worker'){
                sh 'mvn compile'
            }
        }
    }

    post{
        always{
            echo "build pipeline completed"
        }
    }
}