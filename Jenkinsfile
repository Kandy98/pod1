#!groovy
pipeline { 
    agent any
    stages {
        stage('Preparation') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                bat "dotnet build"
            }
        }
        stage('Test') {
            steps {
                bat "dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover"
            
            }
        }
        stage('SonarQube') {
            steps {
                withSonarQubeEnv('localsonar') {
                     
                    bat "dotnet build-server shutdown"
                    bat """dotnet SonarScanner begin /k:FirstCoreProject /d:sonar.host.url=http://localhost:9000 /d:sonar.cs.opencover.reportsPaths="./FirstCoreProject/coverage.opencover.xml" /d:sonar.coverage.exclusions="**Test*.cs"""
                    bat "dotnet build FirstSolution.sln"
                    bat """dotnet SonarScanner end /d:sonar.login="0f8ce5d785371f68519444d69760b7e0bc3a0ede"""
                    
                }
            }
        }
        stage("Quality Gate") {
            steps {
              timeout(time: 5, unit: 'MINUTES') {
                waitForQualityGate abortPipeline: true
              }
            }
        }
        stage('Run') {
            steps {
                
                bat "cd FirstCoreProject"
                bat "dotnet run --project FirstCoreProject"
            }
        }
        stage('Publish'){
            steps{
                bat "dotnet publish"
            }
        }
    }
    post {  
        always {  
            echo 'Build Result:'  
        }  
        success {  
            echo 'The .net build was successful !'  
        }  
        failure {  
            echo 'The .net build failed !'  
        }    
    }
}
