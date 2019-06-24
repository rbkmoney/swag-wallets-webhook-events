#!groovy
build('swag-wallets-webhook-events', 'docker-host') {
  checkoutRepo()
  loadBuildUtils()

  def pipeDefault
  def gitUtils
  runStage('load pipeline') {
    env.JENKINS_LIB = "build_utils/jenkins_lib"
    pipeDefault = load("${env.JENKINS_LIB}/pipeDefault.groovy")
    gitUtils = load("${env.JENKINS_LIB}/gitUtils.groovy")
  }

  pipeDefault() {
    runStage('init') {
      withGithubSshCredentials {
          sh 'make wc_init'
      }
    }

    runStage('build') {
      sh 'make wc_build'
    }

    // Java
    runStage('Execute build container') {
      withCredentials([[$class: 'FileBinding', credentialsId: 'java-maven-settings.xml', variable: 'SETTINGS_XML']]) {
         if (env.BRANCH_NAME == 'master' || env.BRANCH_NAME.startsWith('epic/')) {
           sh 'make SETTINGS_XML=${SETTINGS_XML} BRANCH_NAME=${BRANCH_NAME} wc_java.deploy'
         } else {
           sh 'make SETTINGS_XML=${SETTINGS_XML} wc_java.compile'
         }
      }
    }
  }
}