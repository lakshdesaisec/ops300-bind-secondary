pipeline {
    agent { label 'podman' }

    triggers {
        pollSCM('* * * * *')
    }

    environment {
        REGISTRY = 'registry.assign.lndesai.300.ops:5000'
        IMAGE    = "${REGISTRY}/bind-secondary"
        TAG      = "${env.BUILD_NUMBER}"
    }

    stages {
        stage('Build') {
            steps {
                sh 'podman build -t "$IMAGE:$TAG" .'
            }
        }
        stage('CVE Scan') {
            steps {
                // Decision: collect results as an artifact and continue.
                // Every build saves trivy-report.txt with all HIGH/CRITICAL findings.
                sh 'rm -f image.tar && podman save -o image.tar "$IMAGE:$TAG"'
                sh 'trivy image --input image.tar --severity HIGH,CRITICAL --ignore-unfixed --exit-code 0 --format table --output trivy-report.txt'
                archiveArtifacts artifacts: 'trivy-report.txt', fingerprint: true
            }
        }
        stage('Push') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'registry-creds', usernameVariable: 'REG_USER', passwordVariable: 'REG_PASS')]) {
                    sh 'echo "$REG_PASS" | podman login --username "$REG_USER" --password-stdin "$REGISTRY"'
                    sh 'podman push "$IMAGE:$TAG"'
                }
            }
        }
    }

    post {
        always {
            sh 'podman logout "$REGISTRY" || true'
            sh 'rm -f image.tar'
        }
    }
}
