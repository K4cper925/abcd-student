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
                    git credentialsId: 'github-pat', url: 'https://github.com/K4cper925/abcd-student', branch: 'secrets'
                    echo "Code checked out. Listing workspace contents..."
                    sh 'ls -al ${WORKSPACE}'
                    echo "Waiting for 5 seconds..."
                    sleep(5)
                }
            }
        }

        stage('Looking for secrets in repository') {
            steps {
                sh("trufflehog git https://github.com/K4cper925/abcd-student.git --branch main --only-verified --json > trufflehog_report.json || true")
            }
        }
        stage('Verify if file exist') {
            steps {
                sh 'ls -al trufflehog_report.json'
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
                echo "Checking if OSV report exists..."
                if (fileExists('trufflehog_report.json')) {
                    echo "Sending OSV report to DefectDojo..."
                    defectDojoPublisher(artifact: 'trufflehog_report.json',
                                        productName: 'Juice Shop',
                                        scanType: 'Trufflehog Scan',
                                        engagementName: 'kacper.czerwinski925@wp.pl')
                } else {
                    echo "OSV report not found, skipping DefectDojo upload."
                }
            }

        }
    }
}
