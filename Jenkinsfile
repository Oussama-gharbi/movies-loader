def imageName = 'movies-loader'
def password = '*YT87az$$'
def registry = 'oussamagharbi'

pipeline{

    agent any
environment {
        tag = sh(returnStdout: true, script: "git rev-parse --short=10 HEAD").trim()
    }
    stages{
        stage('Checkout'){
            steps{
               git branch: 'develop', url: 'https://github.com/Oussama-gharbi/movies-loader.git'
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
                           //   sh "docker login -u oussamagharbi -p ${password}"
                   withCredentials([usernamePassword(credentialsId: 'docker_hub', passwordVariable: 'PASS', usernameVariable: 'USER')]) {
                   sh 'echo $PASS | docker login -u $USER --password-stdin'
                 }
                  echo ' Docker push...'
                  sh "docker tag movies-loader oussamagharbi/movies-loader:${tag}"
                  sh "docker push oussamagharbi/movies-loader:${tag}"
                    //docker.build(imageName)
                }
            }
        }
        
        stage('Analyze'){
            def scannedImage = "${registry}/${imageName}:${tag} ${workspace}/Dockerfile"
            writeFile file: 'images', text: scannedImage
            anchore name: 'images'
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
