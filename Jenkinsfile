pipeline { 
    agent any  

    tools { 
        maven 'mvn' 
    }

    stages { 

        stage('Init') {
            steps {
                rtServer (
                    id: 'artifactory-eu',
                    url: 'http://10.1.0.11/artifactory',
                    username: 'admin',
                    password: 'password',
                    timeout: 60
                )
            }
        }

        stage('Build') { 
            steps {
                rtBuildInfo (
                    captureEnv: true,
                    buildName: 'maven-sample-with-violations'
                )      
                rtMavenRun (
                    tool: 'mvn',
                    pom: 'pom.xml',
                    goals: 'clean package -DskipTests=true',
                    buildName: 'maven-sample-with-violations'
                )
            }
        }

        stage ('Issues Collection') {
            steps {
                rtCollectIssues (
                    serverId: 'artifactory-eu',
                    config: """{
                        "version": 1,
                        "issues": {
                            "trackerName": "JIRA",
                            "regexp": "(.+-[0-9]+)\\s-\\s(.+)",
                            "keyGroupIndex": 1,
                            "summaryGroupIndex": 2,
                            "trackerUrl": "https://jira.foo.com",
                            "aggregate": "true",
                            "aggregationStatus": "RELEASED"
                        }
                    }""",

                    buildName: 'maven-sample-with-violations'
                )
            }
        }
        
        stage('Publish Build Info') {
            steps {
                rtPublishBuildInfo (
                    serverId: 'artifactory-eu',
                    buildName: 'maven-sample-with-violations'
                )
            }
        }

        stage('Scan') {
            steps {
                xrayScan (
                    serverId: 'artifactory-eu',
                    buildName: 'maven-sample-with-violations',
                    failBuild: true
                )
            }
        }
    }

}
