@Library('my-shared-library') _
pipeline{
    agent any
    stages{    
        stage('Git Checkout'){
        
            steps{
            gitCheckout(
                branch: "master",
                url: "https://github.com/wissem007/awdevops_java_app.git"
            )
            }
        }
        stage('Unit Test maven'){

            steps{
               script{
                   
                   mvnTest()
               }
            }
        }
        stage('Integration Test maven'){
            steps{
               script{
                   
                   mvnIntegrationTest()
               }
            }
        }
    }
}