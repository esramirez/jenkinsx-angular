pipeline {
  agent {
    label "jenkins-nodejs"
  }
  environment {
    ORG = 'esramirez-gmail-com'
    APP_NAME = 'jenkinsx-angular'
    CHARTMUSEUM_CREDS = credentials('jenkins-x-chartmuseum')
    DOCKER_REGISTRY_ORG = 'esramirez@gmail.com'
  }
  stages {
    stage('CI Build and push snapshot') {
      when {
        branch 'PR-*'
      }
      environment {
        PREVIEW_VERSION = "0.0.0-SNAPSHOT-$BRANCH_NAME-$BUILD_NUMBER"
        PREVIEW_NAMESPACE = "$APP_NAME-$BRANCH_NAME".toLowerCase()
        HELM_RELEASE = "$PREVIEW_NAMESPACE".toLowerCase()
      }
      steps {
        container('jenkinsxio/jx:2.0.119')
        container('nodejs') {
          sh "npm install"
          sh "CI=true DISPLAY=:99 npm test"
        }
      }
    }
    stage('Build Release') {
      when {
        branch 'master'
      }
      steps {
        container('nodejs') {

          // ensure we're not on a detached head
          sh "git checkout master"
          sh "git config --global credential.helper store"
          sh "jx step git credentials"

          // so we can retrieve the version in later steps
          sh "echo \$(jx-release-version) > VERSION"
          sh "jx step tag --version \$(cat VERSION)"
        }
        container('jenkinsxio/jx:2.0.119')
        container('nodejs') {
          sh "npm install"
          sh "CI=true DISPLAY=:99 npm test"
        }
      }
    }
  }
  post {
        always {
          cleanWs()
        }
  }
}
