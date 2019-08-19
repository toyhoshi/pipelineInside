node {
    def app

    stage('Clone repository') {
        /* Let's make sure we have the repository cloned to our workspace */

        checkout scm
    }

    stage('Build image') {
        /* This builds the actual image; synonymous to
         * docker build on the command line, using my docker proxy socket 2375 */
        docker.withServer('tcp://databox.local:2375') {
            app = docker.build("toyhoshi/pipelineinside")
        }
    }

    stage('Test image') {
        /* Ideally, we would run a test framework against our image.
         * For this example, we're using a Volkswagen-type approach ;-) */
         docker.withServer('tcp://databox.local:2375') {
         /*   app.inside { */
                sh 'echo "Tests passed"'
         /*   } */
         }
    }

    stage('Push image') {
        /* Finally, we'll push the image with two tags:
         * First, the incremental build number from Jenkins
         * Second, the 'latest' tag.
         * Pushing multiple tags is cheap, as all the layers are reused. */
        docker.withServer('tcp://databox.local:2375') {
            docker.withRegistry('https://registry.hub.docker.com', 'docker_hub_login') {
                app.push("${env.BUILD_NUMBER}")
                app.push("latest")
            }
        }
    }
}
