#!/usr/bin/env groovy

@Library('Jenkins-shared-library')_

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
                    dockerLogin()
                    dockerPush "marlenadocker/my-nodejs-app:${IMAGE_NAME}"
                }
            }
        }
        stage('Commit version update'){
            steps {
                script {
                    gitAddRemote "@github.com/KacperKlys/DevOps-Bootcamp-ex8.git"
                    gitUserConfig "jenkins"
                    gitPush($BRANCH_NAME, "ci: version bump")
                }
            }
        }
    }
}
