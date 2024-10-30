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
                // Uruchomienie skanowania
                sh '''
                osv-scanner scan --lockfile package-lock.json --json > osv_report.json  
                '''
            }
        }
        stage('Verify if file exist') {
            steps {
                sh 'ls -al osv_report.json'
            }
        }




        //stage('Step 5: Run OSV Scanner') {
        //    steps {
        //        echo "Starting OSV Scanner..."
        //        sh '''
        //            "osv-scanner scan --lockfile ${WORKSPACE}/package-lock.json --json > ${WORKSPACE}/raport_osv.json"
        //        '''
        //        echo "Listing contents of /home/kacper/Documents/DevSecOps/Test/osv_reports directory..."
        //        sh 'ls -al ${WORKSPACE}/raport_osv.json || true'
        //        
        //        
        //        echo "OSV Scanner scan complete. Waiting for 5 seconds..."
        //        sleep(5)
        //    }
        //}

        //stage('Step 6: Verify and Archive Scan Results') {
        //    steps {
        //        echo "Verifying scan results..."
        //        sh '''
        //            cp ${WORKSPACE}/raport_osv.json home/kacper/Documents/DevSecOps/Test/raport_osv.json 
        //            ls -al /home/kacper/Documents/DevSecOps/Test/osv-reports/
        //        '''
        //        echo "Archiving scan results..."
        //        archiveArtifacts artifacts: '/home/kacper/Documents/DevSecOps/Test/osv_reports/**/*', fingerprint: true, allowEmptyArchive: true
//                echo "Scan results archived. Waiting for 5 seconds..."
//                sleep(5)
//            }
//        }
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
                                        engagementName: 'kacperczerwinski925@wp.pl')
                } else {
                    echo "OSV report not found, skipping DefectDojo upload."
                }
            }

            archiveArtifacts artifacts: '/home/kacper/Documents/DevSecOps/Test/**/*', fingerprint: true, allowEmptyArchive: true
        }
    }
}
