pipeline{
    agent any

   stages {
      stage('Verify Branch') {
         steps {
            echo "$GIT_BRANCH"
         }
        }
        stage('Docker Build') {
         steps {
            powershell 'docker images -a'
            powershell '''cd azure-vote/
                docker images -a
                docker build -t jenkins-pipeline .
                docker images -a
                cd ..'''
         }
      }
       stage('Start test app') {
          steps {
             powershell '''
                docker-compose up -d
                ./scripts/test_container.ps1
               Start-Sleep -s 15
             '''
          }
          post {
             success {
                echo "App started successfully :)"
             }
             failure {
                echo "App failed to start :("
             }
          }
       }
    //    stage('Run Tests') {
    //       steps {
    //          powershell 'pytest ./tests/test_sample.py'
    //       }
    //    }
       stage('Stop test app') {
          steps {
             powershell 'docker-compose down'
          }
        }
        stage('Container Push'){
            steps{
                echo "Workspace is $WORKSPACE"
                dir("$WORKSPACE/azure-vote")
                {
                    script{
                        docker.withRegistry('https://index.docker.io/v1/', 'dockerHub')
                        {
                            def image = docker.build('aasheesh123/jenkins:v1')
                            image.push()
                        }
                    }
                }
            }
        }
      //   stage('Deploy to QA')
      //   {
      //      environment{
      //         ENVIRONMENT = 'qa'
      //      }
      //      steps
      //      {
      //         echo "Deploying to $ENVIRONMENT"
      //         acsDeploy(
      //            azureCredentialsId: 'Azure_Access', 
      //            configFilePaths: '**/*.yaml', 
      //            containerService: 'azure-vote-app', 
      //             resourceGroupName: 'Jenkins_To_Kubernetes', 
      //             sshCredentialsId: ''
      //         )
      //      }
       
       stage('RG')
       {
          steps{
          azureCLI commands: [[exportVariablesString: '', script: 'az group create --name AKSDevOps --location eastus']], principalCredentialId: 'Azure_Access'
          }
       }
   }
}
