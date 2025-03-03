pipeline{
    agent any
    environment{
        PATH = "/opt/maven/bin:$PATH"
    }
    stages{
        stage("git clone"){
            steps{
                git url: "https://github.com/PankajSonii/saidemytrend.git", branch: "main"
                
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
    }
}
