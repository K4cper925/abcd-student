pipeline {
    agent any
    options {
        skipDefaultCheckout(true)
    }

    stages {
        stage('Step 1: Verify and Archive Scan Results') {
            steps {
                echo "Verifying scan results..."

            }
        }
    }

    post {
        always {
            script {
                //echo "Cleaning up Docker containers..."
                //sh '''
                //    docker stop zap juice-shop || true
                //    docker rm zap juice-shop || true
                //'''
                //echo "Containers stopped and removed."

                echo "Checking if ZAP XML report exists..."
                echo "Sending ZAP XML report to DefectDojo..."
                defectDojoPublisher(artifact: '/home/kacper/Documents/DevSecOps/Test/reports/zap_xml_report.xml',
                                    productName: 'Juice Shop',
                                    scanType: 'ZAP Scan',
                                    engagementName: 'kacperczerwinski925@wp.pl')     
            }

            archiveArtifacts artifacts: '/home/kacper/Documents/DevSecOps/Test/**/*', fingerprint: true, allowEmptyArchive: true
        }
    }
}
