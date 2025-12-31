#!/usr/bin/env groovy

@Library('Jenkins-shared-library')

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
                    	def packageJSON = readJSON file: 'package.json'
                    	def packageJSONVersion = packageJSON.version
                    	env.IMAGE_NAME = "$packageJSONVersion-$BUILD_NUMBER"
                    }
                }
            }
        }
        stage('Test App') {
            steps {
                script {
                    nodeTest()
                }
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    buildImage "marlenadocker/my-nodejs-app:${IMAGE_NAME}"
                }
            }
        }
        stage('Commit version update'){
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'githubtkn', passwordVariable: 'PASS', usernameVariable: 'USER')]){
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
