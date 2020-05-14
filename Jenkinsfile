pipeline {
    agent {
        label 'windows'
        }
    environment {
        outputFolder = 'artifacts'
        // App folder name MUST match the octopus deploy project name
        appFolderName = 'JenkinsMVCDotNet'
        slnFile = 'JenkinsMVCDotNet.sln'
    }
 
    stages {
 
        stage ('Build') {
 
            agent {
                docker {
                    image 'mcr.microsoft.com/dotnet/framework/sdk:4.8' //Framework V.16
                    // image 'mcr.microsoft.com/dotnet/framework/sdk:4.8-20200211-windowsservercore-ltsc2016' //Framework V.15
                    reuseNode 'true'
                }
            }
            steps{
                powershell """
 
                    nuget.exe restore ${env.slnFile} -Verbosity normal
 
                    # https://docs.microsoft.com/en-us/visualstudio/msbuild/msbuild-command-line-reference?view=vs-2019
 
                    MSBuild.exe ${env.slnFile} /t:Rebuild /p:Configuration=Debug /p:OutputPath=/build /clp:Summary
                     
                    # Copy files from msbuild folder into the output in order for the devops step to zip and deploy
                    # The output folder is depend on what was configure in your sln file
 
                    # Web build will create "_PublishedWebsites" folder
                    Copy-Item -Recurse -Force /build/_PublishedWebsites/* ${WORKSPACE}/${env.outputFolder}/${env.appFolderName}
 
                    # Non web build will not create the sub folder
                    Copy-Item -Recurse -Force /build/* ${WORKSPACE}/${env.outputFolder}/${env.appFolderName}
                     
                    Get-Item ${WORKSPACE}/${env.outputFolder}
                """
 
            }  
        }        
 
 
    // DO NOT ALTER BELOW THIS LINE !!!
    // Mandatory Step to push builds into binary repo and deploy
 
       //stage ('DevOps') {
       //     steps {
       //         devops(env)
       //     }
       // }
    }
}