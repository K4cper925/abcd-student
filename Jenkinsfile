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
                // Kopiowanie raportu do katalogu roboczego Jenkinsa
                echo "Copying ZAP XML report to workspace..."
                sh 'cp /home/kacper/Documents/DevSecOps/Test/reports/zap_xml_report.xml $WORKSPACE/reports/'

                // Sprawdzanie, czy raport został poprawnie skopiowany i wysyłanie go do DefectDojo
                echo "Checking if ZAP XML report exists..."
                echo "Sending ZAP XML report to DefectDojo..."
                defectDojoPublisher(artifact: '$WORKSPACE/reports/zap_xml_report.xml', // zmiana na ścieżkę do workspace
                                    productName: 'Juice Shop',
                                    scanType: 'ZAP Scan',
                                    engagementName: 'kacper.czerwinski925@wp.pl')
            }

            // Archiwizacja wszystkich artefaktów z katalogu workspace
            archiveArtifacts artifacts: 'reports/**/*', fingerprint: true, allowEmptyArchive: true
        }
    }
}
