pipeline {
    agent any

    environment {
        DEPENDENCY_CHECK_DIR = "/opt/dependency-check/bin"
        REPORT_PATH = "dependency-check-report.html"
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', 
                    credentialsId: 'GitHub', 
                    url: 'https://github.com/jeel-chheta/Jenkins-Security-Pipeline.git'
            }
        }

        stage('Run Security Scans') {
            steps {
                script {
                    echo "Running OWASP Dependency-Check..."
                    sh """
                        $DEPENDENCY_CHECK_DIR/dependency-check.sh --project my-app --scan . --format HTML --out $REPORT_PATH
                    """
                }
            }
        }

        stage('Approve/Reject Build') {
            steps {
                script {
                    echo "Checking security scan results..."
                    def vulnerabilities = sh(script: "grep -c '<severity>CRITICAL</severity>' $REPORT_PATH || echo 0", returnStdout: true).trim()

                    if (vulnerabilities.toInteger() > 0) {
                        error "Build rejected due to critical vulnerabilities!"
                    } else {
                        echo "Build approved. No critical vulnerabilities found."
                    }
                }
            }
        }
    }

    post {
        success {
            echo "Security scan passed. Build is compliant!"
        }
        failure {
            echo "Security scan failed. Build rejected!"
        }
    }
}
