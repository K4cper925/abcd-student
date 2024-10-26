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

        //stage('Step 2: Prepare Juice Shop Application for Testing') {
        //    steps {
        //        script {
        //            echo "Starting Juice Shop application..."
        //            sh '''
        //                docker run --name juice-shop -d --rm -p 3000:3000 bkimminich/juice-shop
        //            '''
        //            echo "Juice Shop is running. Waiting for 5 seconds..."
        //            sleep(5)
        //        }
        //    }
        //}

        stage('Step 3: Prepare Directory for Scan Results') {
            steps {
                echo "Creating directory for scan results..."
                sh '''
                    mkdir -p /home/kacper/Documents/DevSecOps/Test/reports
                    chmod -R 777 /home/kacper/Documents/DevSecOps/Test/reports
                '''
                echo "Directory created. Waiting for 5 seconds..."
                sleep(5)
            }
        }

        stage('Step 4: Copy passive.yaml File') {
            steps {
                echo "Copying passive.yaml file from repository to workspace..."
                sh '''
                    cp ${WORKSPACE}/passive_scan.yaml /home/kacper/Documents/DevSecOps/Test/passive_scan.yaml
                '''
                echo "File copied. Waiting for 5 seconds..."
                sleep(5)
            }
        }

        stage('Step 5: Run OWASP ZAP for Passive Scanning') {
            steps {
                echo "Starting OWASP ZAP container with full paths..."
                sh '''
                    docker run --name zap \
                    -v /home/kacper/Documents/DevSecOps/Test:/zap/wrk/:rw \
                    -t ghcr.io/zaproxy/zaproxy:stable bash -c \
                    "zap.sh -cmd -addonupdate; \
                    zap.sh -cmd -addoninstall communityScripts; \
                    zap.sh -cmd -addoninstall pscanrulesAlpha; \
                    zap.sh -cmd -addoninstall pscanrulesBeta; \
                    zap.sh -cmd -autorun /zap/wrk/passive_scan.yaml" || true
                '''
                echo "Listing contents of /home/kacper/Documents/DevSecOps/Test directory..."
                sh 'ls -al /home/kacper/Documents/DevSecOps/Test/'
                echo "Listing contents of /home/kacper/Documents/DevSecOps/Test/reports directory..."
                sh 'ls -al /home/kacper/Documents/DevSecOps/Test/reports/'
                
                echo "Fetching ZAP container logs..."
                sh 'docker logs zap'
                
                echo "OWASP ZAP scan complete. Waiting for 5 seconds..."
                sleep(5)
            }
        }

        stage('Step 6: Verify and Archive Scan Results') {
            steps {
                echo "Verifying scan results..."
                sh '''
                    ls -al /home/kacper/Documents/DevSecOps/Test/reports/
                '''
                echo "Archiving scan results..."
                archiveArtifacts artifacts: '/home/kacper/Documents/DevSecOps/Test/reports/**/*', fingerprint: true, allowEmptyArchive: true
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

                echo "Checking if ZAP XML report exists..."
                if (fileExists('/home/kacper/Documents/DevSecOps/Test/reports/zap_xml_report.xml')) {
                    echo "Sending ZAP XML report to DefectDojo..."
                    defectDojoPublisher(artifact: '/home/kacper/Documents/DevSecOps/Test/reports/zap_xml_report.xml',
                                        productName: 'Juice Shop',
                                        scanType: 'ZAP Scan',
                                        engagementName: 'kacperczerwinski925@wp.pl')
                } else {
                    echo "ZAP XML report not found, skipping DefectDojo upload."
                }
            }

            archiveArtifacts artifacts: '/home/kacper/Documents/DevSecOps/Test/**/*', fingerprint: true, allowEmptyArchive: true
        }
    }
}
