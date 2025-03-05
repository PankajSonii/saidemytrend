def registry = 'https://trialu82pjr.jfrog.io/'

pipeline {
    agent any
    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {
        stage('build') {
            steps {
                sh 'mvn clean deploy -Dmaven.test.skip=true'
            }
        }

        stage("Test") {
            steps {
                sh "mvn surefire-report:report"
            }
        }

        stage('sonarqube-analysis') {
            environment {
                scannerHome = tool 'pankaj-sonar-scanner'
            }

            steps {
                withSonarQubeEnv('pankaj-sonarqube-server') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

        stage('Jar Publish') {
            steps {
                script {
                    def server = Artifactory.newServer url: registry + "/artifactory", credentialsId: "artifact-cred"
                    def properties = "buildid = ${env.BUILD_ID},commitid=${env.GIT_COMMIT}" // Ensure GIT_COMMIT is available
                    def uploadSpec = """
                    {
                        "files": [
                            {
                                "pattern": "target/*.jar", // Adjust if your jar is in a different location
                                "target": "soni-libs-release-local/",
                                "flat": "false",
                                "props": "${properties}",
                                "exclusions": ["*.sha1", "*.md5"]
                            }
                        ]
                    }
                    """

                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)
                }
            }
        }
    }
}
