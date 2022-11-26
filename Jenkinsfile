pipeline {
    agent {
        label 'kmaster'
    }
    environment {
        //docker image name
        DOCKER_IMAGE_NAME = "msbalajeee/train-schedule"
        JAVA_HOME = "/usr/lib/jvm/java-8-oracle/"
        CANARY_REPLICAS = 1
        }
    stages {
       // stage('Copy code from GIT') {
       //     steps {
       //         git 'https://github.com/msbalajeee/cicd-pipeline-train-schedule-autodeploy.git'
       //     }
       // }
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
            }
        }

        stage('Build Docker Image') {
           // when {
        //       branch 'master'
        //    }
            steps {
                
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    app.inside {
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            // when {
            //    branch 'master'
            //}
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")
                    }
                }
            }
        }
        
        stage('Create caranay deployment'){
            steps {
                sh 'kubectl create -f train-schedule-kube-canary.yml'
            }
        }
        
         stage('Create production deployment'){
            steps {
                sh 'kubectl create -f train-schedule-kube.yml'
            }
        }
    }
    
}
