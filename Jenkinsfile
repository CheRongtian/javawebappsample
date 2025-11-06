import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=9dcd5889-4d7f-4b8a-a18a-a05b73eca77b',
        'AZURE_TENANT_ID=4383a0de-f9a6-40ca-a6bc-27374d1ba900']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
   stage('deploy') {
    def resourceGroup = 'jenkins-get-started-rg'
    def webAppName = 'rongtian-cc8-app'
    // login Azure
    withCredentials([usernamePassword(credentialsId: 'AzureServicePrincipal', passwordVariable: 'AZURE_CLIENT_SECRET', usernameVariable: 'AZURE_CLIENT_ID')]) {
        sh '''
          az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
          az account set -s $AZURE_SUBSCRIPTION_ID
        '''
    }
    // deploy via Azure CLI
    sh """
      az webapp deploy \
        --resource-group jenkins-get-started-rg \
        --name rongtian-cc8-app \
        --src-path target/calculator-1.0.war \
        --type war
    """
    sh 'az logout'
    }
  }
}
