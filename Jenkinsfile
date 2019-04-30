pipeline {
    agent any
    tools {
        maven "maven 3.6"
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
                            //sh "mvn clean package checkstyle:checkstyle"
                            echo "failed to be checked"
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





    }
}