pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }

    stages {
        stage('Step 1: Code Checkout') {
            steps {
                script {
                    cleanWs()
                    echo "Checking out code from GitHub repository..."
                    git credentialsId: 'github-pat', url: 'https://github.com/K4cper925/abcd-student', branch: 'semgrep'
                    echo "Code checked out. Listing workspace contents..."
                    sh 'ls -al ${WORKSPACE}'
                    echo "Waiting for 5 seconds..."
                    sleep(5)
                }
            }
        }

        stage('Run SAST scan with auto semgrep') {
            steps {
                sh("semgrep scan --config auto --json > semgrep_report.json || true")
            }
        }
        stage('Verify if file exist') {
            steps {
                sh 'ls -al semgrep_report.json'
            }
        }
    }

    post {
        always {
            script {
		sh '''
			pwd
			ls -la
		'''
                echo "Checking if semgrep report exists..."
                if (fileExists('semgrep_report.json')) {
                    echo "Sending semgrep report to DefectDojo..."
                    defectDojoPublisher(artifact: 'semgrep_report.json',
                                        productName: 'Juice Shop',
                                        scanType: 'Semgrep JSON Report',
                                        engagementName: 'kacper.czerwinski925@wp.pl')
                } else {
                    echo "OSV report not found, skipping DefectDojo upload."
                }
            }

        }
    }
}
