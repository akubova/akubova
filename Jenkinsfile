library identifier: 'akubova', 
        retriever: modernSCM([$class: 'GitSCMSource', remote: 'https://github.com/akubova/.git'])

pipeline {
    agent any
    parameters {
        booleanParam(name: 'RC', defaultValue: false, description: 'Is this a Release Candidate?')
    }
    environment {
        VERSION = "0.1.0"        
        VERSION_RC = "rc.2"
    }
    stages {
        stage('Audit tools') {                        
            steps {
                auditTools()
            }
        }
        stage('Build') {
            environment {
                VERSION_SUFFIX = getVersionSuffix rcNumber: env.VERSION_RC, isReleaseCandidate: params.RC
            }
            steps {
              echo "Building version: ${VERSION} with suffix: ${VERSION_SUFFIX}"
              sh 'dotnet build -p:VersionPrefix="${VERSION}" --version-suffix "${VERSION_SUFFIX}" ./m3/src/Pi.Web/Pi.Web.csproj'
            }
        }
        stage('Unit Test') {
            steps {
              dir('./m3/src') {
                bat '''
                    dotnet test --logger "trx;LogFileName=Pi.Math.trx" Pi.Math.Tests/Pi.Math.Tests.csproj
                    dotnet test --logger "trx;LogFileName=Pi.Runtime.trx" Pi.Runtime.Tests/Pi.Runtime.Tests.csproj
                '''
                mstest testResultsFile:"**/*.trx", keepLongStdio: true
              }
            }
        }
        stage('Smoke Test') {
            steps {
              bat 'dotnet ./m3/src/Pi.Web/bin/Debug/netcoreapp3.1/Pi.Web.dll'
            }
        }
        stage('Publish') {
            when {
                expression { return params.RC }
            } 
            steps {
                bat 'dotnet publish -p:VersionPrefix="${VERSION}" --version-suffix "${VERSION_RC}" ./m3/src/Pi.Web/Pi.Web.csproj -o ./out'
                archiveArtifacts('out/')
            }
        }
    }
}
