def registry = 'https://triale7vkk5.jfrog.io/'

pipeline{
    agent any
    environment{
        PATH = "/opt/maven/bin:$PATH"
    }
    
    stages{
        stage('git clone'){
            steps{
                git url: 'https://github.com/PankajSonii/saidemytrend.git', branch: 'main' 
            }
        }
        
        stage("build"){
            steps{
                sh "mvn clean package"
            }
        }
        
        stage('sonarqube-analysis'){
            
            environment{
                scannerHome = tool 'pankaj-sonar-scanner'
            }
            
            steps{
                
                withSonarQubeEnv('pankaj-sonarqube-server'){
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage('Jar Publish'){
            steps{
                script{
                    def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "artifact-cred"
                    def properties = "buildid = ${env.BUILD_ID},commitid=${GIT_COMMIT}"
                    def uploadSec = """ {
                    " files" : [
                    {
                    "pattern": "jarstaging/(*)",
                    "target": "soni-libs-release-local/{1}",
                    "flat" : "false",
                    "props" : "${properties}",
                    "exclusions" : [" *.sha1", "*.md5"]
                    }
                    ]
                    } """

                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)

                }
            }
        }
    }
}
                    
    

