node('docker-build') { // Use the label here
    def app

    stage('Clone repository') {
        checkout scm
    }

    // This block tells Jenkins: "Run these commands inside the docker container we defined"
    container('docker') {
        stage('Build image') {
            app = docker.build("denmgarcia/test")
        }

        stage('Test image') {
            app.inside {
                sh 'echo "Tests passed"'
            }
        }

        stage('Push image') {
            docker.withRegistry('https://registry.hub.docker.com', 'dockerhub') {
                app.push("${env.BUILD_NUMBER}")
            }
        }
    }

    stage('Trigger ManifestUpdate') {
        echo "triggering updatemanifestjob"
        build job: 'updatemanifest', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
    }
}