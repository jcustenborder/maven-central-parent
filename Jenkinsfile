#!groovy
package com.github.jcustenborder.jenkins.pipeline

properties([
        buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', daysToKeepStr: '', numToKeepStr: '10'))
])


def execute() {
    node {
        deleteDir()
        checkout scm

        stage('build') {
            docker.image(images.jdk8_docker_image).inside {
                withCredentials([file(credentialsId: 'gpg_pubring', variable: 'GPG_PUBRING'), file(credentialsId: 'gpg_secring', variable: 'GPG_SECRING')]) {
                    configFileProvider([configFile(fileId: 'mavenSettings', variable: 'MAVEN_SETTINGS')]) {
                        def mvn = new MavenUtilities(env, steps, MAVEN_SETTINGS, GPG_PUBRING, GPG_SECRING)

                        def goals
                        def profiles = null

                        if (env.BRANCH_NAME == 'master') {
                            goals = 'clean deploy'
                            profiles = 'gpg-signing,maven-central'
                        } else {
                            goals = 'clean package'
                        }

                        mvn.execute(goals, profiles)
                    }
                }
            }
        }
    }
}