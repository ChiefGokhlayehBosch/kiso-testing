pipeline
{
    agent
    {
        docker
        {
            image 'kiso-build-env:v0.0.6'
        }
    }

    stages
    {
        stage('Setup Env')
        {
            environment {
                WORKON_HOME = "/tmp/.venvs"
                PIPENV_CACHE_DIR = "/tmp/.cache"
            }

            steps
            {
                script
                {
                    echo "Build Pipenv"
                    sh 'pipenv --python 3'
                    sh 'pipenv install --dev'
                }
            }
        }
        stage('Format check')
        {
            steps
            {
                script
                {
                    echo "Run different format checks: TODO"
                }
            }
        }
        stage('Run unittests')
        {
            environment {
                WORKON_HOME = "/tmp/.venvs"
                PIPENV_CACHE_DIR = "/tmp/.cache"
            }

            steps
            {
                script
                {
                    echo "Run all the unittests"
                    sh 'pipenv run invoke unittest'
                    // TODO: call coverage report + save it
                }
            }
        }
        stage('Run virtual-test')
        {
            steps
            {
                script
                {
                    echo "Run a virtual communication: TODO"
                }
            }
        }
        stage('Generate documentation')
        {
            environment {
                WORKON_HOME = "/tmp/.venvs"
                PIPENV_CACHE_DIR = "/tmp/.cache"
            }

            steps
            {
                script
                {
                    echo "Generate documentation"
                    sh 'pipenv run invoke docs'
                }
            }
        }
        stage('Release')
        {
            when
            {
                buildingTag()
            }
            parallel
            {
                stage('Release documentation')
                {
                    steps
                    {
                        script
                        {
                            echo "Release documentation on readthedocs.org: TODO"
                        }
                    }
                }
                stage('Release package')
                {
                    steps
                    {
                        script
                        {
                            echo "Release package on PyPI.org: TODO"
                        }
                    }
                }
            } // parallel
        } // Release
    } // stages

    post // Called at very end of the script to notify developer and github about the result of the build
    {
        always
        {
            archiveArtifacts (
                artifacts: 'TBD',
                fingerprint: true
            )
            junit (
                allowEmptyResults: true,
                testResults: 'TBD'
            )
        }
        success
        {
            archiveArtifacts (
                artifacts: 'builddir-debug/docs/doxygen/**, builddir-unittests/*_cov/**, docs/website/public/**/*',
                fingerprint: true
            )
            cleanWs()
        }
        unstable
        {
            notifyFailed()
        }
        failure
        {
            notifyFailed()
        }
        aborted
        {
            notifyAbort()
        }
    }
} // pipeline


def notifyFailed()
{
    emailext (subject: "Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) is failing",
                body: "Oups, something went wrong with ${env.BUILD_URL}... We are looking forward for your fix!",
                recipientProviders: [[$class: 'CulpritsRecipientProvider'],
                                    [$class: 'DevelopersRecipientProvider'],
                                    [$class: 'RequesterRecipientProvider']])
}

def notifyAbort()
{
    emailext (subject: "Job '${env.JOB_NAME}' (${env.BUILD_NUMBER}) was aborted",
                body: "Oups, something went wrong with ${env.BUILD_URL}... We are looking forward for your fix!",
                recipientProviders: [[$class: 'CulpritsRecipientProvider'],
                                    [$class: 'DevelopersRecipientProvider'],
                                    [$class: 'RequesterRecipientProvider']])
}
