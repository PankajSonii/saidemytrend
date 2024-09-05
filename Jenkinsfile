pipeline {
    // Specify the agent to run the pipeline
    agent any
    // Set environment variables for the pipeline
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    // Define the stages of the pipeline
    stages {
	
	stage('checkout'){
	
	steps{
	//clone the git repo
	git url:'https://github.com/PankajSonii/saidemytrend.git', branch: 'main'
	}
}	
	
        // Stage for building the project
        stage("build") {
            steps {
               
                echo "----------- build started ----------"
                
                sh 'mvn clean deploy'
                
                echo "----------- build completed ----------"
            }
        }

       

        // Stage for SonarQube analysis
        stage('SonarQube analysis') {
            environment {
                // Set the SonarQube scanner tool
                scannerHome = tool 'soni-sonar-scanner'
            }
            steps {
                // Execute SonarQube analysis within the SonarQube environment
                withSonarQubeEnv('soni-sonarqube-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }
	}
}
