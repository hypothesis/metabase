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
        deployApp(image: img, app: 'metabase', env: 'qa', region: 'us-west-1')
    }

    milestone()
    stage('prod deploy') {
        input(message: "Deploy to prod?")
        milestone()
        deployApp(image: img, app: 'metabase', env: 'prod', region: 'us-west-1')
    }
}
