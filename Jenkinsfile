pipeline {
    agent any
    environment {
        //be sure to replace "bhavukm" with your own Docker Hub username
        DOCKER_IMAGE_NAME = "vijayandevopshere/train-schedule"
        //sample
    }
    stages {
        stage('Build') {
            steps {
                echo 'Running build automation'
                sh './gradlew build --no-daemon'
                archiveArtifacts artifacts: 'dist/trainSchedule.zip'
                echo 'build automation completed'
            }
        }
        stage('Build Docker Image') {
            //when {
              //  branch 'master'
            //}
            steps {
                echo 'Inside Docker build'
                script {
                    app = docker.build(DOCKER_IMAGE_NAME)
                    echo 'After Docker.build'
                    app.inside {
                        echo 'After App.Inside'
                        sh 'echo Hello, World!'
                    }
                }
            }
        }
        stage('Push Docker Image') {
            //when {
              //  branch 'master'
           // }
            steps {
                script {
                    echo 'Begin Push Docker'
                    docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                   // withCredentials([usernameColonPassword(credentialsId: 'docker_hub_login', variable: 'docker_hub_login')]) {
                  //  withCredentials([usernamePassword(credentialsId: 'docker_hub_login', passwordVariable: 'docker__hub_login', usernameVariable: 'docker__hub_login')]) {
                        //echo 'Before login'
                       // sh 'docker login -u vijayandevopshere -p ${docker_hub_login}'
                         echo 'After login'
                        //sh 'docker image push app.push("${env.BUILD_NUMBER}")
                        app.push("${env.BUILD_NUMBER}")
                        echo 'After Push1'
                        app.push("latest")
                        echo 'After Push2'
                    }
                }
            }
        }
        //stage('CanaryDeploy') {
            //when {
              //  branch 'master'
            //}
          //  environment { 
            //    CANARY_REPLICAS = 1
            //}
          //  steps {
            //    kubernetesDeploy(
              //      kubeconfigId: 'kubeconfig',
                //    configs: 'train-schedule-kube-canary.yml',
                  //  enableConfigSubstitution: true
                //)
            //}
        //}
        stage('DeployToProduction') {
            //when {
              //  branch 'master'
            //}
            environment { 
                CANARY_REPLICAS = 0
            }
            steps {
                echo 'Deploy to Production?'
                sshagent(['kube_master']) {
          sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/cicd-pipeline-train-schedule-autodeploy/train-schedule-kube.yml root@43.205.213.69:/opt/yml'
          sh 'scp -o StrictHostKeyChecking=no /var/lib/jenkins/workspace/cicd-pipeline-train-schedule-autodeploy/train-schedule-kube-canary.yml root@43.205.213.69:/opt/yml'
                script
                    {
                        try{
                        sh "ssh root@43.205.213.69:/opt/yml kubectl apply -f ."
                        }
                        catch(error){
                         sh "ssh root@43.205.213.69:/opt/yml kubectl create -f ."
                        }
                    }
                }
                
                //milestone(1)
                //kubernetesDeploy(
                  //  kubeconfigId: 'kubeconfig',
                   // configs: 'train-schedule-kube-canary.yml',
                    //enableConfigSubstitution: true
                //)
                //kubernetesDeploy(
                  //  kubeconfigId: 'kubeconfig',
                    //configs: 'train-schedule-kube.yml',
                    //enableConfigSubstitution: true
                //)
            }
        }
    }
}
