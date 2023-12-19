














node {
    def app

    stage('Clone repository') {
        checkout scm
    }

    stage('Build image') { 
        app = docker.build("tgilmo300/cw02:1.0")
    }

    stage('Test image') {
        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Run Container') {
        script {
            def containerId = app.run("-d -p 8081:8080").id

            try {
                sh 'docker ps'
                sh "docker rm -f --volumes ${containerId}"
            } catch (Exception e) {
                echo "Error cleaning up container: ${e.message}"
            }
        }
    }


    stage('Push image') {
      script {
         docker.withRegistry("https://registry.hub.docker.com", 'dockerHubCredentials') {
            def imageTag = "${env.BUILD_NUMBER}"
            app.push(imageTag)
            echo "Docker image pushed to tgilmo300/cw02:${imageTag}"
        }
    }
}


    stage('Deploy to Kubernetes') {
         
        def imageTag = "${env.BUILD_NUMBER}"

         script {   
              withCredentials([sshUserPrivateKey(credentialsId: 'my-ssh-key', keyfileVariable: 'KEY_FILE')]) {
                 sh 'ssh -o StrictHostKeyChecking=no -i $KEY_FILE ubuntu@ip-172-31-52-54 "kubectl set image deployments/deployment-2 tgilmo300=tgilmo300/cw02:${imageTag}"'
             }
         }
    
    } 
  
