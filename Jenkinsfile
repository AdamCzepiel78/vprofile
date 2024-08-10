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
    }

    stages {
        stage("Build") {
            steps {
                sh "mvn -s settings.xml -DskipTests install"
            }
            post {
                success {
                    echo "Now Archiving."
                    archiveArtifacts artifacts: '**/*.war'
                }
            }
        }
    }
}