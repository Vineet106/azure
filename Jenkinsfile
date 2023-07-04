import groovy.json.JsonSlurper

def getFtpPublishProfile(def publishProfilesJson) {
  def pubProfiles = new JsonSlurper().parseText(publishProfilesJson)
  for (p in pubProfiles)
    if (p['publishMethod'] == 'FTP')
      return [url: p.publishUrl, username: p.userName, password: p.userPWD]
}

node {
  withEnv(['AZURE_SUBSCRIPTION_ID=5f397c40-6821-42b8-ac8d-eae44f88c577',
        'AZURE_TENANT_ID=80b21fbb-67eb-42f5-8bda-d85b82dc64ee']) {
    stage('init') {
      checkout scm
    }
  
    stage('build') {
      sh 'mvn clean package'
    }
  
    stage('deploy') {
      def resourceGroup = 'workshop'
      def webAppName = 'vineet106'
      // login Azure
      withCredentials([usernamePassword(credentialsId: 'holyhell', passwordVariable: 'Vux8Q~p5CwlRtpppZtxu5LMPQ~-yABRTAKr-Ab5-', usernameVariable: '84dcc845-9575-4949-bb98-eb6ec1c2abb1')]) {
       sh '''
          az login --service-principal -u 84dcc845-9575-4949-bb98-eb6ec1c2abb1 -p Vux8Q~p5CwlRtpppZtxu5LMPQ~-yABRTAKr-Ab5- -t 0b21fbb-67eb-42f5-8bda-d85b82dc64ee
          az account set -s 5f397c40-6821-42b8-ac8d-eae44f88c577
        '''
      }
      // get publish settings
      def pubProfilesJson = sh script: "az webapp deployment list-publishing-profiles -g $resourceGroup -n $webAppName", returnStdout: true
      def ftpProfile = getFtpPublishProfile pubProfilesJson
      // upload package
      sh "curl -T target/calculator-1.0.war $ftpProfile.url/webapps/ROOT.war -u '$ftpProfile.username:$ftpProfile.password'"
      // log out
      sh 'az logout'
    }
  }
}
