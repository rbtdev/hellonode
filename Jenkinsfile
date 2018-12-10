
node {
    notifyStarted()
    try {
        def app
        
        stage('Clone repository') {
            /* Let's make sure we have the repository cloned to our workspace */

            checkout scm
        }

        stage('Build image') {
            /* This builds the actual image; synonymous to
            * docker build on the command line */

            app = docker.build("rbtdev/hellonode")
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
            docker.withRegistry('https://registry.hub.docker.com', 'docker-hub-credentials') {
                app.push("${env.BUILD_NUMBER}")
                app.push("latest")
            }
        }
        notifyComplete()
    }
    catch (e) {
        currentBuild.result = "FAILED"
        notifyFailed(e)
        throw e
    }
}

def notifyStarted() {
        // send to Slack
        slackSend (color: '#FFFF00', message: "STARTED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
def notifyComplete() {
        // send to Slack
        slackSend (color: '#00FF00', message: "COMPLETED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})")
        }
def notifyFailed(e) {
         slackSend (color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL}) ${e.message}")
        }