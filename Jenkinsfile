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
            timeout(time: 10, unit: 'MINUTES') {
               waitForQualityGate abortPipeline: true
            }
        }
    }
}