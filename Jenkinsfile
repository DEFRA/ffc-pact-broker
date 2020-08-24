@Library('defra-library@v-8') _

def chartName = 'ffc-pact-broker'
def tag = '2.0.0'
def repoName = ''

node {
  checkout scm
  try {    
    stage('Set variables') {
      repoName = utils.getRepoName()
    }

    stage('Helm lint') {
      test.lintHelm(repoName)
    }

    stage('Publish chart') {
      helm.publishChart(DOCKER_REGISTRY, repoName, tag, 'acr', false)
    }
  } catch(e) {

    stage('Send build failure slack notification') {
      notifySlack.buildFailure(e.message, '#generalbuildfailures')
    }

    throw e
  }
}
