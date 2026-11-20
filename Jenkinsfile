pipeline {
    agent any

    options {
        skipDefaultCheckout(true)   // code is fetched manually in the Checkout stage
        buildDiscarder(logRotator(numToKeepStr: '20'))
    }

    parameters {
        string(name: 'REPO_URL',
               defaultValue: 'https://github.com/ThisIsMikeyS/cicd-demo-app',
               description: 'HTTPS link to the Git repository (your Fork)')
        string(name: 'GIT_BRANCH',
               defaultValue: 'master',
               description: 'Branch to build')
        booleanParam(name: 'RUN_TESTS',
                     defaultValue: true,
                     description: 'Run unit tests')
    }

    stages {

        stage('Checkout') {
            steps {
                deleteDir()   // clean workspace so old files never leak into a build
                git url: params.REPO_URL, branch: params.GIT_BRANCH
                script {
                    env.COMMIT_SHA = sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
                }
                echo "Branch : ${params.GIT_BRANCH}"
                echo "Commit : ${env.COMMIT_SHA}"
            }
        }

        stage('Validate') {
            steps {
                sh '''
                    set -e
                    missing=0
                    for f in README.md requirements.txt; do
                        if [ -f "$f" ]; then echo "OK   file $f"; else echo "FAIL file $f is missing"; missing=1; fi
                    done
                    for d in src tests; do
                        if [ -d "$d" ]; then echo "OK   dir  $d/"; else echo "FAIL dir  $d/ is missing"; missing=1; fi
                    done
                    if [ "$missing" -ne 0 ]; then
                        echo "Project structure validation FAILED"
                        exit 1
                    fi
                    echo "Project structure validation passed"
                '''
            }
        }

        stage('Test') {
            when {
                expression { return params.RUN_TESTS }
            }
            steps {
                sh 'python3 -m unittest discover -s tests -v'
            }
        }

        stage('Build') {
            steps {
                sh '''
                    set -e
                    {
                        echo "Build number: ${BUILD_NUMBER}"
                        echo "Commit SHA:   ${COMMIT_SHA}"
                    } > build-info.txt
                    cat build-info.txt
                    tar -czf app.tar.gz \
                        --exclude=.git --exclude=__pycache__ --exclude=app.tar.gz \
                        src tests README.md requirements.txt build-info.txt
                    echo "Archive contents:"
                    tar -tzf app.tar.gz
                '''
            }
        }

        stage('Archive') {
            steps {
                archiveArtifacts artifacts: 'app.tar.gz, build-info.txt', fingerprint: true
            }
        }
    }

    post {
        success {
            echo "BUILD SUCCESS: pipeline #${env.BUILD_NUMBER} finished successfully."
        }
        failure {
            echo "BUILD FAILURE: pipeline #${env.BUILD_NUMBER} failed. See the failed stage above."
        }
    }
}

