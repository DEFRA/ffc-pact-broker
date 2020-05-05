@Library('defra-library@4') _

def chartName = 'ffc-pact-broker'
def tag = '1.0.0'
def repoName = ''

node {
  checkout scm
  try {
    stage('Set GitHub status as pending') {
      build.setGithubStatusPending()
    }
    stage('Set variables') {
      repoName = build.getRepoName()
    }
    stage('Helm lint') {
      test.lintHelm(repoName)
    }
    stage('Publish chart') {
      withCredentials([
        usernamePassword(credentialsId: 'artifactory-credentials', usernameVariable: 'username', passwordVariable: 'password')
      ]) {
        sh "helm package ../helm/$chartName --dependency-update"
        sh "curl -u $username:$password -X PUT ${ARTIFACTORY_REPO_URL}ffc-helm-local/$chartName-${tag}.tgz -T $chartName-${tag}.tgz"
      }
    }
    stage('Set GitHub status as success'){
      build.setGithubStatusSuccess()
    }    
  }
  catch(e) {
    build.setGithubStatusFailure(e.message)
    notifySlack.buildFailure(e.message, "#generalbuildfailures")
    throw e
  }
}
