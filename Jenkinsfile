pipeline {
    agent any

    tools {
        maven "MAVEN3"
        jdk "OracleJDK8"
    }

    environment{
      NEXUS-GRP-REPO = "vpro-maven-group"
      NEXUSIP = "172.31.28.180"
      NEXUSPORT = "8081"
      SNAPREPO = "vprofile-snapshot"
      RELEASE-REPO = "vprofile-release"
      CENTRAL-REPO = "vpro-maven-central"
      NEXUS-USER = "admin"
      NEXUS-PASS = "G1i2z3z4m5o6h7()=?"
      NEXUS-LOGIN = "nexuslogin"
    }

    stages{
        stage("Build"){
            steps {
                sh "mvn -s settings.xml -DskipTests install"
            }
        }
    }
}