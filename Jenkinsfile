node('local-node-mac') {

        stage('Clone repository') {
            checkout scm
        }
        stage('SonarQube analysis') {

                withSonarQubeEnv('SonarQube') {
                    sh "mvn clean package sonar:sonar"
                }

        }
        stage("Quality Gate"){
            timeout(time: 1, unit: 'HOURS') {
                def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
            }
        }

    try {
        stage('Start Testing'){
            sh "mvn clean verify"
        }

        currentBuild.result = "SUCCESS"
    } catch (e) {
      // If there was an exception thrown, the build failed
        currentBuild.result = "FAILED"
        throw e

    } finally {
        publishReport();
    }

}

def publishReport(){
       publishHTML(target: [
        reportName : 'Serenity',
        reportDir:   'target/site/serenity',
        reportFiles: 'index.html',
        keepAll:     true,
        alwaysLinkToLastBuild: true,
        allowMissing: false
    ])
}
