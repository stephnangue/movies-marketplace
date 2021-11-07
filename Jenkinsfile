

node('workers'){
    stage('Checkout'){
        checkout scm
    }

    stage('Static Code Analysis'){ 
        withSonarQubeEnv('sonarqube') {
            sh 'sonar-scanner -Dsonar.projectVersion=$BUILD_NUMBER'
        }
    }

    stage('Quality Gate'){ 
        timeout(time: 5, unit: 'MINUTES'){
            def qg = waitForQualityGate()
            if (qg.status != 'OK') {
                error "Pipeline aborted due to quality gate failure: ${qg.status}"
            }
        }
    }

}