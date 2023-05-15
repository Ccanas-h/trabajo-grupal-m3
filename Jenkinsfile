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
                        -Dsonar.projectKey=pipeline-jenkinsfile-grupo5 \
                        -Dsonar.projectName=pipeline-jenkinsfile-grupo5 \
                        -Dsonar.scm.disabled=true \
                        -Dsonar.sources=. \
                        -Dsonar.language=java \
                        -Dsonar.java.binaries=./target/classes \
                        -Dsonar.sourceEncoding=UTF-8 \
                        -Dsonar.host.url=https://3445-190-20-203-11.ngrok-free.app \
                        -Dsonar.exclusions=src/test/java/****/*.java \
                        -Dsonar.login=sqp_0abf452fc61e4fd347a8de023746be991c305499"
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
                            nexusUrl: "192.168.1.126:8081",
                            groupId: pom.groupId,
                            version: pom.version,
                            repository: "pipeline-jenkinsfile-grupo5",
                            credentialsId: "nexus-id-cristobal",
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
        success {
            slackSend channel: "#canal-slack", token: "canal-slack-canas2", color: 'good', message: "${custom_msg1()}", tokenCredentialId: 'canal-slack-canas2', teamDomain: 'canas-workspace', username: 'jenkins'
        }
        failure{
            slackSend channel: "#canal-slack", token: "canal-slack-canas2", color: "danger", message: "${custom_msg()}", tokenCredentialId: 'canal-slack-canas2', teamDomain: 'canas-workspace', username: 'jenkins'
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
    def custom_msg1() {
        def JENKINS_URL= "localhost:8080"
        def JOB_NAME = env.JOB_NAME
        def BUILD_ID= env.BUILD_ID
        def JENKINS_LOG= " SUCCESS: Job [${env.JOB_NAME}] Logs path: ${JENKINS_URL}/job/${JOB_NAME}/${BUILD_ID}/consoleText"
        return JENKINS_LOG
    }
    
