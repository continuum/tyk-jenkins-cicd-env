pipeline {
    agent any

    environment {
        tyk2_dashboard_credentials = credentials('tyk2-dashboard-credentials')
    }

    stages {
        stage('deploy') {
            when {
                expression { env.BRANCH_NAME == 'main' }
            }
            steps {
                script {
                    if (fileExists('.tyk.json')) {
                        echo "Deploying from ${env.BRANCH_NAME}"
                        sh "./tyk-sync sync -d http://localhost:4000 -s ${env.tyk2_dashboard_credentials} -p ."
                    } else {
                        echo 'No files to deploy'
                    }
                }
            }
        }
    }

    post {
        always {
            deleteDir()
        }
    }
}
