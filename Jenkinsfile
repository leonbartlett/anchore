pipeline {
    agent any
    stages {
        stage('build') {
            steps {
                sh'''
                    echo 'FROM debian:latest’ > Dockerfile
                    echo ‘CMD ["/bin/echo", "HELLO WORLD...."]' >> Dockerfile
                '''
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub-leonbartlett') {
                        def image = docker.build('leonbartlett/myrepo:latest')
                        image.push()
                    }
                }
            }
        }
        stage('analyze') {
            steps {
                sh 'echo "docker.io/exampleuser/examplerepo:latest `pwd`/Dockerfile" > anchore_images'
                anchore name: 'anchore_images'
            }
        }
        stage('teardown') {
            steps {
                sh'''
                    for i in `cat anchore_images | awk '{print $1}'`;do docker rmi $i; done
                '''
            }
        }
    }
}
