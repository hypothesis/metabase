#!groovy

@Library('pipeline-library') _

def img

node {
    stage('build') {
        checkout(scm)
        img = buildApp(name: 'hypothesis/metabase')
    }

    onlyOnMain {
        stage('release') {
            releaseApp(image: img)
        }
    }
}

onlyOnMain {
    milestone()
    stage('qa deploy') {
        deployApp(image: img, app: 'metabase', env: 'qa', region: 'us-west-1')
    }

    milestone()
    stage("approval") {
        input(message: "Proceed to production deploy?")
    }

    milestone()
    stage("prod Deploy") {
        parallel(
            us_metabase: {
                deployApp(image: img, app: "metabase", env: "prod", region: "us-west-1")
            },
            ca_metabase: {
                // Workaround to ensure all parallel builds happen. See https://hypothes-is.slack.com/archives/CR3E3S7K8/p1625041642057400
                sleep 2
                deployApp(image: img, app: "metabase", env: "prod", region: "ca-central-1")
            }
        )
    }
}
