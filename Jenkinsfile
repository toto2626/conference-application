pipeline {
    agent any
    tools {
        maven "maven 3.6"
    }
    environment {
        NEXUS_ARTIFACT_VERSION= "${env.BUILD_NUMBER}"
    }
    options {
        parallelsAlwaysFailFast()
    }
    stages {
        stage('Non-Parallel Stage') {
            steps {
                echo 'This stage will be executed first.'
            }
        }
        stage('Parallel Stage') {
            parallel {
                   stage('Checkstyle') {
                        steps{
                            // Run the maven build with checkstyle
                            // sh "mvn clean  -Dmaven.test.skip=true package checkstyle:checkstyle"
                            echo " checkstyle error"
                         }
                     }
                    stage('Sonarqube') {
                        steps {
                            withSonarQubeEnv('SonarQube') {
                            sh "mvn  clean package sonar:sonar -Dsonar.host_url=$SONAR_HOST_URL "

                            }
                         }
                    }
            }

        }
        stage('Publish in Nexus') {
            steps {
                nexusPublisher nexusInstanceId: 'Nexus',
                nexusRepositoryId: 'releases',
                packages: [[$class: 'MavenPackage',
                mavenAssetList: [[classifier: '', extension: '', filePath: 'target/conference-app-3.0.0.war']], mavenCoordinate: [artifactId: 'de.codecentric', groupId: 'conference-app', packaging: 'war', version: NEXUS_ARTIFACT_VERSION]]]
            }
        }
        stage('Build image') {
            steps{
                script{
                    def customImage = docker.build("conference-project")
                }
            }
        }
        stage('Run uat image') {
            steps{
                sh 'docker ps -f name=conference-uat -q | xargs --no-run-if-empty docker container stop'
                sh 'docker container ls -a -fname=conference-uat -q | xargs -r docker container rm'
                sh 'docker run -d --name conference-uat -p 8590:8080 conference-project'
            }
        }












    }
}