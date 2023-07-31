@Library('my-shared-library') _
pipeline{
    agent any

    parameters{

        choice(name: 'action', choices: 'create\ndelete', description: 'Choose create/Destroy')
        string(name: 'ImageName', description: "name of the docker build", defaultValue: 'javapp')
        string(name: 'ImageTag', description: "tag of the docker build", defaultValue: 'v1')
        string(name: 'portMapping', description: "name of the Application", defaultValue: '8080')
        string(name: 'DockerHubUser', description: "name of the Application", defaultValue: 'wissem007')
         
    }

    stages{    
        stage('Git Checkout'){
            when { expression {  params.action == 'create' } }
        
            steps{
            gitCheckout(
                branch: "master",
                url: "https://github.com/wissem007/awdevops_java_app.git"
            )
            }
        }
        // stage('Unit Test maven'){
        //     when { expression {  params.action == 'create' } }

        //     steps{
        //        script{
                   
        //            mvnTest()
        //        }
        //     }
        // }
        // stage('Integration Test maven'){
        //     when { expression {  params.action == 'create' } }
        //     steps{
        //        script{
                   
        //            mvnIntegrationTest()
        //        }
        //     }
        // }
        // stage('Static code analysis: Sonarqube'){
        //     when { expression {  params.action == 'create' } }
        //     steps{
        //        script{
                   
        //            def SonarQubecredentialsId = 'sonarqube-api'
        //            statiCodeAnalysis(SonarQubecredentialsId)
        //        }
        //     }
        // }
        // stage('Quality Gate Status Check : Sonarqube'){
        //  when { expression {  params.action == 'create' } }
        //     steps{
        //        script{
                   
        //            def SonarQubecredentialsId = 'sonarqube-api'
        //            QualityGateStatus(SonarQubecredentialsId)
        //        }
        //     }
        // }

        stage('Maven Build : maven'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   mvnBuild()
               }
            }
        }
        stage('Docker Image Build'){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                // dockerBuild("${params.ImageName}","${params.ImageTag}","${params.AppName}")
                   
                dockerBuild("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }
        stage('Docker Image Scan: trivy '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImageScan("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }

        stage('Docker Image Push : DockerHub '){
         when { expression {  params.action == 'create' } }
            steps{
               script{
                   
                   dockerImagePush("${params.ImageName}","${params.ImageTag}","${params.DockerHubUser}")
               }
            }
        }

        stage('Docker Image Cleanup : DockerHub') {
            when { expression { params.action == 'create' } }
            steps {
                script {
                    // Vérifier si l'image existe avant de la supprimer
                    def imageNameV1 = "${params.ImageName}:v1"
                    def imageNameLatest = "${params.ImageName}:latest"

                    def imageV1Exists = sh(returnStatus: true, script: "docker images -q ${imageNameV1}").trim()
                    def imageLatestExists = sh(returnStatus: true, script: "docker images -q ${imageNameLatest}").trim()

                    if (imageV1Exists) {
                        // L'image v1 existe, alors on peut la supprimer
                        dockerImageCleanup("${params.ImageName}", "v1", "${params.DockerHubUser}")
                    } else {
                        echo "L'image ${imageNameV1} n'existe pas. Aucune suppression nécessaire."
                    }

                    if (imageLatestExists) {
                        // L'image latest existe, alors on peut la supprimer
                        dockerImageCleanup("${params.ImageName}", "latest", "${params.DockerHubUser}")
                    } else {
                        echo "L'image ${imageNameLatest} n'existe pas. Aucune suppression nécessaire."
                    }
                }
            }
        }

    

        stage('Run Docker Container') {
            when { expression { params.action == 'create' } }
                steps {
                  script {
                    dockerImageRun("${params.ImageName}", "${params.ImageTag}", "${params.portMapping}")
        }
           }
        }


   }
}