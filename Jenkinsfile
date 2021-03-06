pipeline { 
   agent { 
       docker { 
           image 'mcr.microsoft.com/dotnet/core/sdk:3.1' 
       } 
   } 
   environment { 
       DOTNET_CLI_HOME = "/tmp/DOTNET_CLI_HOME" 
       def BUILDVERSION = sh(script: "echo `date +%F-%T`", returnStdout: true).trim() 
   } 
   stages { 
       stage('Restore packages') { 
          steps { 
             sh 'dotnet restore WebApplication.sln' 
          } 
       } 
       stage('Clean') { 
          steps { 
               sh 'dotnet clean WebApplication.sln --configuration Release' 
          } 
       } 
 
    stage('Build') { 
       steps { 
          sh 'dotnet build WebApplication.sln --configuration Release --no-restore' 
          } 
       }
 stage('Test: Unit Test') { 
    steps { 
        sh 'dotnet test XUnitTestProject/XUnitTestProject.csproj --configuration Release --no-restore' 
      }
    }
 
    stage('Publish') { 
       steps { 
           sh 'dotnet publish WebApplication/WebApplication.csproj --configuration Release --no-restore' 
          } 
        } 
    stage('Archive') { 
       steps { 
           sh 'tar -cvzf publish.tar.gz --strip-components=1 WebApplication/bin/Release/netcoreapp3.1/publish' 
           archive 'publish.tar.gz' 
          } 
       } 



    stage('Deploy Stage') {
      steps { 
      sh 'ls -a'
      timeout(time: 200, unit: 'SECONDS') {
          dir('WebApplication/bin/Release/netcoreapp3.1/publish') {
          sh 'ls' 
          sh 'cp ../../../../../manifest.yml manifest.yml' 
          pushToCloudFoundry(
              target: 'https://api.cf.us10.hana.ondemand.com/',
               organization: '5aece873trial',
               cloudSpace: 'dev',
                credentialsId: 'AhmedCf',
               )
        }
      }
    }
   }
  }
}
   
