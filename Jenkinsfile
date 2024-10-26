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
                echo "Checking if ZAP XML report exists..."
                echo "Sending ZAP XML report to DefectDojo..."
                
                // Zaktualizuj ścieżkę do pliku raportu
                defectDojoPublisher(
                    artifact: '/var/jenkins_home/workspace/ABCD-pipeline/reports/zap_xml_report.xml',
                    productName: 'Juice Shop',
                    scanType: 'ZAP Scan',
                    engagementName: 'kacper.czerwinski925@wp.pl'
                )
            }

            // Zarchiwizuj inne pliki
            archiveArtifacts artifacts: '/var/jenkins_home/workspace/ABCD-pipeline/**/*', fingerprint: true, allowEmptyArchive: true
        }
    }
}
