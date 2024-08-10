pipeline {
    agent any

    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }

    environment{
      NEXUS_GRP_REPO = "vpro-maven-group"
      NEXUSIP = "172.31.27.120"
      NEXUSPORT = "8081"
      SNAPREPO = "vprofile-snapshot"
      RELEASE_REPO = "vprofile-release"
      CENTRAL_REPO = "vpro-maven-central"
      NEXUS_USER = "admin"
      NEXUS_PASS = "G1i2z3z4m5o6h7()=?"
      NEXUS_LOGIN = "nexuslogin"
      SONARSERVER = "sonarserver"
      SONARSCANNER = "sonarscanner"
    }

    stages {
        stage("Build") {
            steps {
                sh "mvn -s settings.xml -DskipTests install"
            }
            post {
                success {
                    echo "Now Archiving Artifact."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
        stage("Test"){
            steps{
                sh "mvn -s settings.xml test"
            }
        }
        stage("Checkstyle Analysis"){
            steps {
                sh "mvn -s settings.xml checkstyle:checkstyle"
            }
        }

        stage("Sonar Analysis"){
            environment {
                scannerHome = tool "${SONARSCANNER}"
            }
            steps{
                withSonarQubeEnv("${SONARSERVER}") {
                    sh '''${scannerHome}/bin/sonar-scanner -Dsonar.projectKey=vprofile \
                        -Dsonar.projectName=vprofile-repo \
                        -Dsonar.projectVersion=1.0 \
                        -Dsonar.sources=src/ \
                        -Dsonar.java.binaries=target/test-classes/com/visualpathit/account/controllerTest/ \
                        -Dsonar.junit.reportsPath=target/surefire-reports/ \
                        -Dsonar.jacoco.reportsPath=target/jacoco.exec \
                        -Dsonar.java.checkstyle.reportPaths=target/checkstyle-result.xml'''
                }
            }
        }

        stage("Quality Gate"){
            steps{
                timeout(time: 1, unit: "HOURS"){
                    waitForQualityGate abortPipeline: true
                }
            }
        }

        stage("Upload Artifact"){
            steps{
                nexusArtifactUploader(
                    nexusVersion: 'nexus3',
                    protocol: 'http',
                    nexusUrl: "${NEXUSIP}:${NEXUSPORT}",
                    groupId: 'QA',
                    version: "${env.BUILD_ID}-${env.BUILD_TIMESTAMP}",
                    repository: "${RELEASE_REPO}",
                    credentialsId: "${NEXUS_LOGIN}",
                    artifacts: [
                        [artifactId: 'vproapp',
                        classifier: '',
                        file: 'target/vprofile-v2.war',
                        type: 'war']
                    ]
                )
            }
        }
        post {
            success {
                discordSend description: "Jenkins Pipeline Build", footer: "Status Message", link: env.BUILD_URL, result: currentBuild.currentResult, title: JOB_NAME, webhookURL: "https://discordapp.com/api/webhooks/1271745570083569684/mM5d6nwXOIUhf11Ohy47B_GeUSG6bYip2mQbt0R2IwqPRwuSl3QLBF2hpMZOXRYe9TzB"
            }
            failure {
                discordSend description: "Jenkins Pipeline Build", footer: "Status Message", link: env.BUILD_URL, result: currentBuild.currentResult, title: JOB_NAME, webhookURL: "https://discordapp.com/api/webhooks/1271745570083569684/mM5d6nwXOIUhf11Ohy47B_GeUSG6bYip2mQbt0R2IwqPRwuSl3QLBF2hpMZOXRYe9TzB"
            }
        }
    }
}