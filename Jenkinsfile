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
                    git credentialsId: 'github-pat', url: 'https://github.com/K4cper925/abcd-student', branch: 'osv'
                    echo "Code checked out. Listing workspace contents..."
                    sh 'ls -al ${WORKSPACE}'
                    echo "Waiting for 5 seconds..."
                    sleep(5)
                }
            }
        }

        stage('Run osv-scanner') {
            steps {
                sh("osv-scanner scan --lockfile package-lock.json --json > osv_report.json || true")
            }
        }
        stage('Verify if file exist') {
            steps {
                sh 'ls -al osv_report.json'
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
                if (fileExists('osv_report.json')) {
                    echo "Sending OSV report to DefectDojo..."
                    defectDojoPublisher(artifact: 'osv_report.json',
                                        productName: 'Juice Shop',
                                        scanType: 'OSV Scan',
                                        engagementName: 'kacper.czerwinski925@wp.pl')
                } else {
                    echo "OSV report not found, skipping DefectDojo upload."
                }
            }

        }
    }
}
