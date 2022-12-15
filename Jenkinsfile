   node {
      stage('init') {
        checkout scm
      }
      stage('build') {
        dir("/var/lib/jenkins/workspace/Deploy-DemoApp/complete") {
                sh 'mvn clean package'
        }
      }
      stage('deploy') {
        withCredentials([azureServicePrincipal('azure_service_principal')]) {
          // Log in to Azure
          sh '''
            az login --service-principal -u $AZURE_CLIENT_ID -p $AZURE_CLIENT_SECRET -t $AZURE_TENANT_ID
            az account set -s $AZURE_SUBSCRIPTION_ID
          '''  
          // Set default resource group name and service name. Replace <resource group name> and <service name> with the right values
          sh 'az config set defaults.group=jenkins'
          sh 'az config set defaults.spring=cicd-service'

          // Deploy applications
          sh 'az config set extension.use_dynamic_install=yes_without_prompt'
          //sh 'az spring app deploy --jar-path ./complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar'
          sh 'az spring app deploy -n gs-demo -s cicd-service -g jenkins --jar-path ./complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar'
          sh 'az logout'
        }
      }
    }
