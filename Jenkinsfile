ef imageName = 'movies-loader'
def registry = 'https://registry.slowcoder.com'

pipeline{
    agent any

    stages{
        stage('Checkout'){
            steps{
                checkout scm
            }
        }

        stage('Unit Tests'){
            steps{
                script {
                    def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")
                    imageTest.inside{
                        sh "python test_main.py"
                    }
                }
            }
        }

        stage('Build'){
            steps{
                script {
                   
                   echo 'building docker image...'
                   sh 'docker build -t movies-loader .'
                    echo ' docker login...'
                   withCredentials([usernamePassword(credentialsId: 'docker_hub_token', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                 sh "echo $PASS | docker login -u $USER --password-stdin"
                 }
                  echo ' Docker push...'
                  sh "docker tag movies-loader oussamagharbi/movies-loader:${commitID}"
                  sh "docker push oussamagharbi/movies-loader:${commitID}"
                    docker.build(imageName)
                }
            }
        }

        /*stage('Push'){
            steps{
                docker.withRegistry(registry, 'registry') {
                        docker.image(imageName).push(commitID())

                        if (env.BRANCH_NAME == 'develop') {
                            docker.image(imageName).push('develop')
                        }
                }
            }
        }*/
    }
}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}