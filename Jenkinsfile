pipeline {
     agent any
     stages {
        stage("Checkout") {
            steps {
                git url: 'https://github.com/webdev1401/calculator.git',
                branch: 'master'
            }
        }
        stage("Compile") {
            steps {
                sh "./gradlew compileJava"
            }
        }
        stage("Unit test") {
            steps {
                sh "./gradlew test"
            }
        }
        stage("Code coverage") {
            steps {
                sh "./gradlew jacocoTestReport"
                publishHTML (target: [
                    reportDir: 'build/reports/jacoco/test/html',
                    reportFiles: 'index.html',
                    reportName: "JaCoCo Report"
                ])
                sh "./gradlew jacocoTestCoverageVerification"
            }
        }
        stage("Static code analysis") {
            steps {
                sh "./gradlew checkstyleMain"
                publishHTML (target: [
                    reportDir: 'build/reports/checkstyle/',
                    reportFiles: 'main.html',
                    reportName: "Checkstyle Report"
                ])
            }
        }
        stage("Package") {
            steps {
                sh "./gradlew build"
            }
        }
        stage("Docker build") {
            steps {
                sh "docker build -t localhost:443/calculator ."
            } 
        }
        stage("Docker login") {
            steps {
                sh "docker login --username ammar --password azerty12345"
            } 
        }
        stage("Docker push") {
            steps {
                sh "docker push localhost:443/calculator"
            }
        }
        stage("Deploy to staging") {
            steps {
                sh "docker run -d --rm -p 8765:8080 --name calculator localhost:443/calculator"
            } 
        }
    }
    post {
        always {
            mail to: 'webdev@prototyp.fr',
            subject: "Completed Pipeline: ${currentBuild.fullDisplayName}",
            body: "Your build completed, please check: ${env.BUILD_URL}"
        }
    }
}