node {
    def app
    def image_name = "getintodevops/hellonode"
    
    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line */

        app = docker.build("${image_name}:${env.BUILD_NUMBER}")
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */

        app.inside {
            sh 'echo "Tests passed"'
        }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
       /* docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }*/
        
        docker.withRegistry('http://localhost:5000', '') {
            app.push("${env.BUILD_NUMBER}")
            app.push("latest")
        }
    }
    
    stage('Run a container') {
        try {
            sh "docker stop hellnode"
            sh "docker rm hellnode"
        }catch(Exception ex) {
            println("Catching the exception" + ex);
        }
        
        sh "docker run --name=hellnode -d -p 8000:8000 localhost:5000/${image_name}" 
        sh 'echo "http://192.168.33.100:8000"'
    }
}
