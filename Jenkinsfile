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

                echo "Copying OSV report to workspace..."
                //sh 'cp /home/kacper/Documents/DevSecOps/abcd-student/raport_osv.json $WORKSPACE/reports/'
                sh 'ls -la'
                
                defectDojoPublisher(artifact: '$WORKSPACE/raport_osv.json',
                                        productName: 'Juice Shop',
                                        scanType: 'OSV Scan',
                                        engagementName: 'kacperczerwinski925@wp.pl')
                echo "dd"
          
            }

            archiveArtifacts artifacts: '/home/kacper/Documents/DevSecOps/Test/**/*', fingerprint: true, allowEmptyArchive: true
        }
    }
}
