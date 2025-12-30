pipeline {
    agent any
    tools {
        nodejs 'nodejs'
    }
    stages {
        stage('Increment version') {
            steps {
                script {
                    echo 'incrementing app version...'
                    dir("app") {
			 sh 'npm version minor --no-git-tag-version'
                    }
                    def packageJSON = readJSON file: 'package.json'
                    def packageJSONVersion = packageJSON.version
                    env.IMAGE_NAME = "$packageJSONVersion-$BUILD_NUMBER"
                }
            }
        }
        stage('Test App') {
            steps {
                script {
                    echo 'testing the application...'
                    dir("app") {
                        sh 'npm install'
                        sh 'npm run test'
                    }
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    echo 'building the application image...'
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-repo', passwordVariable: 'PASS', usernameVariable: 'USER')]){
                        sh "docker build -t marlenadocker/my-nodejs-app:${IMAGE_NAME} ."
                        sh 'echo $PASS | docker login -u $USER --password-stdin'
                        sh "docker push marlenadocker/my-nodejs-app:${IMAGE_NAME}"
                    }
                }
            }
        }
        stage('Commit version update'){
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'github', passwordVariable: 'PASS', usernameVariable: 'USER')]){
                        sh 'git config --global user.email "jenkins@example.com"'
                        sh 'git config --global user.name "jenkins"'

                        sh "git remote set-url origin https://${USER}:${PASS}@github.com/KacperKlys/DevOps-Bootcamp-ex8.git"
                        sh 'git add app/package.json'
                        sh 'git commit -m "ci: version bump"'
                        sh 'git push origin HEAD:main'
                    }
                }
            }
        }
    }
}
