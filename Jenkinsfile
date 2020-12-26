pipeline {
    agent any

    environment {
        TYK_DASH_SECRET = credentials('tyk-dash-secret')
    }

    stages {
        stage('test') {
            steps {
                script {
                    def apiDefs = findFiles(glob: 'api-*.json')

                    apiDefs.each { apiDef ->
                        def pJson = readJSON file: env.WORKSPACE + "/" + apiDef.name
                        println "Testing ${pJson}"
                    }
                }
            }
        }
        stage('deploy') {
            when {
                expression { env.BRANCH_NAME == 'main' }
            }
            steps {
                echo "Deploying, because we are on ${env.BRANCH_NAME}"
                sh "docker run --rm \
                    --mount type=bind,source=\"$(pwd)\",target=/opt/tyk-sync/tmp \
                    tykio/tyk-sync:v1.2.0 \
                    sync \
                    -d=\"http://localhost:4000\" \
                    -s=\"${env.TYK_DASH_SECRET}\" \
                    -b=\"refs/heads/main\" https://github.com/continuum/tyk-jenkins-cicd-env"
            }
        }
    }

    post {
        always {
            deleteDir()
        }
    }
}

def static assertAuthenticated(api) {
    assert api.use_keyless == false : "api ${api.name} should have authentication enabled"
}

def static assertWhitelistedTag(api) {
    assert api.tags.size() > 0 : "api ${api.name}  should be tagged either internal, external or both"

    api.tags.each { tag ->
        assert ["internal", "external"].contains(tag) : "api ${api.name} contains unknown tag ${tag}"
    }
}