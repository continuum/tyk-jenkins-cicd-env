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
                        sh "docker run --rm --mount type=bind,source=\"${pwd()}\",target=/opt/tyk-sync/tmp tykio/tyk-sync:v1.2.0 sync -d=\"http://localhost:4000\" -s=\"${env.tyk2_dashboard_credentials}\" -b=\"refs/heads/main\" https://github.com/continuum/tyk-jenkins-cicd-env"
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
