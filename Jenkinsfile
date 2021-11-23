def semanticVersion = "${env.BUILD_NUMBER}.0.0"
def packageName = "devops_pipeline_demo_${env.BUILD_NUMBER}"
def version = "${env.BUILD_NUMBER}.0"
pipeline { 
    agent any
    tools { 
        maven 'Maven' 
    }
    environment {
        SCANNER_HOME = tool 'sonarScanner'
    }
    stages {
        stage("Build") {
            steps {
                echo "Building..."
                sh 'mvn clean install -DskipTests=true'
            }
        }
        stage("SoftwareQuality") {
            stages {
                stage("SoftwareQuality-dev") {
                    steps {
                        echo "Software Quality Dev..."
                        sonarTest()
                    }
                }
                stage("SoftwareQuality-stage") {
                    stages {
                        stage("SoftwareQuality-stage-win") {
                            steps {
                                echo "Software Quality Stage Win..."
                                sonarTest()
                            }
                        }
                        stage("SoftwareQuality-mac") {
                            steps {
                                echo "Software Quality Stage Mac..."
                                sonarTest()
                            }
                        }
                    }
                }
                stage("SoftwareQuality-prod") {
                    stages {
                        stage("SoftwareQuality-prod-win") {
                            parallel {
                                stage("SoftwareQuality-prod-win-octacore") {
                                    steps {
                                        echo "Software Quality Prod Win octacore..."
                                        sonarTest()
                                    }
                                }
                            }
                        }
                        stage("SoftwareQuality-prod-mac") {
                            stages {
                                stage("SoftwareQuality-prod-mac-octacore") {
                                    steps {
                                        echo "Software Quality Prod mac octacore..."
                                        sonarTest()
                                    }
                                }
                                stage("SoftwareQuality-prod-mac-dualcore") {
                                    steps {
                                        echo "Software Quality Prod Mac dualcore..."
                                        sonarTest()
                                    }
                                }
                            }
                        }
                    }
                }
            }
        }
        stage("Test") {
            steps {
            	echo "Testing..."
                sh "mvn test -Dpublish"
                junit "**/target/surefire-reports/*.xml"
            }
        }
        stage("Deploy") {
            steps {
                echo "Deploying.."
                snDevOpsArtifact(artifactsPayload:"""{"artifacts": [
                    {"name": "demo1.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo1"},
                    {"name": "demo2.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo2"},
                    {"name": "demo3.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo3"},
                    {"name": "demo4.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo4"},
                    {"name": "demo5.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo5"}]}""")
                }
        }
        stage("Prod") {
            steps {
            	echo "Prod deploy..."
                snDevOpsPackage(name: "${packageName}", artifactsPayload: """{"artifacts": [
                    {"name": "demo1.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo1"},
                    {"name": "demo2.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo2"},
                    {"name": "demo3.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo3"},
                    {"name": "demo4.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo4"},
                    {"name": "demo5.jar","version": "${version}","semanticVersion": "${semanticVersion}","repositoryName": "demo5"}]}""")
            }
        }
    }
}
def sonarTest() {
    withSonarQubeEnv('SonarQube_Cloud') {
	    sh '${SCANNER_HOME}/bin/sonar-scanner -Dproject.settings=${SCANNER_HOME}/conf/sonar-scanner.properties'
    }
}
