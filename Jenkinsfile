#!groovy

@Library('pipeline-library') _

def img

node {
    stage('build') {
        checkout(scm)
        img = buildApp(name: 'hypothesis/metabase')
    }

    onlyOnMaster {
        stage('release') {
            releaseApp(image: img)
        }
    }
}

onlyOnMaster {
    milestone()
    stage('qa deploy') {
        lock(resource: 'metabase-qa-deploy', inversePrecedence: true) {
            milestone()
            deployApp(image: img, app: 'metabase', env: 'qa')
        }
    }

    milestone()
    stage('prod deploy') {
        input(message: "Deploy to prod?")
        lock(resource: 'metabase-prod-deploy', inversePrecedence: true) {
            milestone()
            deployApp(image: img, app: 'metabase', env: 'prod')
        }
    }
}
