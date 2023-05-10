pipeline {
    agent any
        stages {
        stage('Build') {
            steps {
                sh 'mvn -B package'
            }
        }
        stage('SonarQube analysis') {
            steps {
                script {
                    sh "/var/jenkins_home/sonar/bin/sonar-scanner \
                        -Dsonar.projectKey=grupo-05 \
                        -Dsonar.projectName=grupo-05 \
                        -Dsonar.scm.disabled=true \
                        -Dsonar.sources=. \
                        -Dsonar.language=java \
                        -Dsonar.java.binaries=./target/classes \
                        -Dsonar.sourceEncoding=UTF-8 \
                        -Dsonar.host.url=https://ae1a-186-105-120-165.ngrok-free.app \
                        -Dsonar.exclusions=src/test/java/****/*.java \
                        -Dsonar.login=squ_79146dd17e54d3df5f3e98170b18069c3c46de0b"
                }
            }
        }
        stage("Publish to Nexus Repository Manager") {
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader(
                            nexusVersion: "nexus3",
                            protocol: "http",
                            nexusUrl: "https://a2b4-181-161-20-134.ngrok-free.app",
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: "grupo5-hosted",
                            credentialsId: "grupo_5",
                            artifacts: [
                                    [artifactId: pom.artifactId,
                                    classifier: '',
                                    file: artifactPath,
                                    type: pom.packaging]
                                ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
        }
    post{
    //     succeed {
    //        slackSend channel: '#fundamentos-de-devops', token: "slack_token", color: 'good', , message: "${custom_msg()}", teamDomain: 'sustantivagrupo', tokenCredentialId: 'slack_token', username: 'Luis_Rivas'
    //    }
        failure{
            slackSend channel: "#devops", token: "pNWA0uvG8p3yK9Xcra0oK7aJ", color: "danger", message: "${custom_msg()}", teamDomain: 'prueba-4vd5809', username: 'Luis_Rivas'
        }
    }
}
    def custom_msg() {
        def JENKINS_URL= "localhost:8080"
        def JOB_NAME = env.JOB_NAME
        def BUILD_ID= env.BUILD_ID
        def JENKINS_LOG= " FAILED: Job [${env.JOB_NAME}] Logs path: ${JENKINS_URL}/job/${JOB_NAME}/${BUILD_ID}/consoleText"
        return JENKINS_LOG
    }
    
