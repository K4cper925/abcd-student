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
                    git credentialsId: 'github-pat', url: 'https://github.com/K4cper925/abcd-student', branch: 'main'
                    echo "Code checked out. Listing workspace contents..."
                    sh 'ls -al ${WORKSPACE}'
                    echo "Waiting for 5 seconds..."
                    sleep(5)
                }
            }
        }


        stage('Step 2: Prepare Directory') {
            steps {
                echo "Creating directory..."
                sh '''
                    mkdir -p /home/kacper/Documents/DevSecOps/Test/osv_reports
                    chmod -R 777 /home/kacper/Documents/DevSecOps/Test/osv_reports
                    osv-scanner --version
                '''
                echo "Directory created. Waiting for 5 seconds..."
                sleep(5)
            }
        }

        //stage('Step 4: Copy passive.yaml File') {
        //    steps {
        //        echo "Copying passive.yaml file from repository to workspace..."
        //        sh '''
        //            cp ${WORKSPACE}/passive_scan.yaml /home/kacper/Documents/DevSecOps/Test/passive_scan.yaml
        //        '''
        //        echo "File copied. Waiting for 5 seconds..."
        //        sleep(5)
        //    }
        //}

        stage('Step 5: Run OSV Scanner') {
            steps {
                echo "Starting OSV Scanner..."
                sh '''
                    "osv-scanner scan --lockfile ${WORKSPACE}/package-lock.json --json > ${WORKSPACE}/raport_osv.json"
                '''
                echo "Listing contents of /home/kacper/Documents/DevSecOps/Test/osv_reports directory..."
                sh 'ls -al ${WORKSPACE}/raport_osv.json || true'
                
                
                echo "OSV Scanner scan complete. Waiting for 5 seconds..."
                sleep(5)
            }
        }

        stage('Step 6: Verify and Archive Scan Results') {
            steps {
                echo "Verifying scan results..."
                sh '''
                    cp ${WORKSPACE}/raport_osv.json home/kacper/Documents/DevSecOps/Test/raport_osv.json 
                    ls -al /home/kacper/Documents/DevSecOps/Test/osv-reports/
                '''
                echo "Archiving scan results..."
                archiveArtifacts artifacts: '/home/kacper/Documents/DevSecOps/Test/osv_reports/**/*', fingerprint: true, allowEmptyArchive: true
                echo "Scan results archived. Waiting for 5 seconds..."
                sleep(5)
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

                echo "Checking if OSV report exists..."
                if (fileExists('${WORKSPACE}/raport_osv.json')) {
                    echo "Sending OSV report to DefectDojo..."
                    defectDojoPublisher(artifact: '${WORKSPACE}/raport_osv.json',
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
