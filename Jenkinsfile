def imageName = 'stephnangue/movies-marketplace'
def registry = '982039600869.dkr.ecr.eu-central-1.amazonaws.com/stephnangue/movies-marketplace'
def credentials = 'ecr:eu-central-1:jenkins_aws_id'

node('workers'){
    stage('Checkout'){
        checkout scm
    }

    def imageTest= docker.build("${imageName}-test", "-f Dockerfile.test .")

    stage('Quality Tests'){
        sh "docker run --rm ${imageName}-test npm run lint"
    }


    stage('Unit Tests'){
        sh "docker run --rm -v $PWD/coverage:/app/coverage ${imageName}-test npm run test"
        publishHTML (target: [
            allowMissing: false,
            alwaysLinkToLastBuild: false,
            keepAll: true,
            reportDir: "$PWD/coverage/marketplace",
            reportFiles: "index.html",
            reportName: "Coverage Report"
        ])
    }

/*
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
*/

    stage('Build'){
        docker.build(imageName)
    }

    stage('Push'){
        docker.withRegistry("https://"+"${registry}","${credentials}") {
            docker.image(imageName).push(commitID())

            if (env.BRANCH_NAME == 'develop') {
                docker.image(imageName).push('develop')
            }
        }    
    }

}

def commitID() {
    sh 'git rev-parse HEAD > .git/commitID'
    def commitID = readFile('.git/commitID').trim()
    sh 'rm .git/commitID'
    commitID
}