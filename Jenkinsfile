@Library('defra-library@v-6') _

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
      repoName = utils.getRepoName()
    }

    stage('Helm lint') {
      test.lintHelm(repoName)
    }

    stage('Publish chart') {
      helm.publishChart(DOCKER_REGISTRY, repoName, tag, 'acr')
    }

    stage('Set GitHub status as success'){
      build.setGithubStatusSuccess()
    }
  } catch(e) {
    stage('Set GitHub status as fail') {
      build.setGithubStatusFailure(e.message)
    }

    stage('Send build failure slack notification') {
      notifySlack.buildFailure(e.message, '#generalbuildfailures')
    }

    throw e
  }
}
