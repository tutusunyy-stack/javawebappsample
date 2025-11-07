import groovy.json.JsonSlurper

node {
  withEnv([
    'AZURE_SUBSCRIPTION_ID=af355983-3ac3-4f8e-acf8-a361f9d56699',
    'AZURE_TENANT_ID=e8a3aab3-9141-4d8b-bbbb-49211b815ec1'
  ]) {
    stage('init') {
      checkout scm
    }

    stage('build') {
      sh 'mvn clean package'
    }

    stage('deploy') {
      def resourceGroup = 'jenkins-get-started-rg'
      def webAppName = 'jenkins-get-started-webapp'

      withCredentials([
        usernamePassword(
          credentialsId: 'AzureServicePrincipal',
          usernameVariable: 'AZURE_CLIENT_ID',
          passwordVariable: 'AZURE_CLIENT_SECRET'
        )
      ]) {
        sh '''
          set -e
          echo "🔹 Logging into Azure..."
          az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
          az account set -s $AZURE_SUBSCRIPTION_ID

          echo "🚀 Deploying WAR to Azure Web App..."
          az webapp deploy \
            --resource-group jenkins-get-started-rg \
            --name jenkins-get-started-webapp \
            --src-path target/calculator-1.0.war \
            --type war

          echo "✅ Deployment completed successfully!"
          az logout
        '''
      }
    }
  }
}
