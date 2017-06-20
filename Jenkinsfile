 env.BUILD_ID = env.BUILD_ID ?: ""
                    env.BUILD_NUMBER = env.BUILD_NUMBER ?: ""
                    env.BUILD_TAG = env.BUILD_TAG ?: ""
                    env.BUILD_URL = env.BUILD_URL ?: ""
                    env.CHANGE_AUTHOR = env.CHANGE_AUTHOR ?: ""
                    env.CHANGE_AUTHOR_DISPLAY_NAME = env.CHANGE_AUTHOR_DISPLAY_NAME ?: ""
                    env.CHANGE_AUTHOR_EMAIL = env.CHANGE_AUTHOR_EMAIL ?: ""
                    env.CHANGE_ID = env.CHANGE_ID ?: ""
                    env.CHANGE_TARGET = env.CHANGE_TARGET ?: ""
                    env.CHANGE_TITLE = env.CHANGE_TITLE ?: ""
                    env.CHANGE_URL = env.CHANGE_URL ?: ""

                    // scm object probably has two UserRemoteConfig objects, one for branches and one for PRs
                    // but both should have the same URL so we will just use the first one
                    // Note: git.GitSCM.getUserRemoteConfigs and git.UserRemoteConfig methods need to be whitelisted on Jenkins master
                    scmURL = scm.getUserRemoteConfigs()[0].getUrl()

                    echo "INFO: BRANCH_NAME [${env.BRANCH_NAME}] BUILD_DISPLAY_NAME [${env.BUILD_DISPLAY_NAME}] BUILD_ID [${env.BUILD_ID}]"
                    echo "INFO: BUILD_NUMBER [${env.BUILD_NUMBER}] BUILD_TAG [${env.BUILD_TAG}] BUILD_URL [${env.BUILD_URL}]"
                    echo "INFO: CHANGE_AUTHOR [${env.CHANGE_AUTHOR}] CHANGE_AUTHOR_DISPLAY_NAME [${env.CHANGE_AUTHOR_DISPLAY_NAME}]"
                    echo "INFO: CHANGE_AUTHOR_EMAIL [${env.CHANGE_AUTHOR_EMAIL}]"
                    echo "INFO: CHANGE_ID [${env.CHANGE_ID}] CHANGE_TARGET [${env.CHANGE_TARGET}]"
                    echo "INFO: CHANGE_TITLE [${env.CHANGE_TITLE}]"
                    echo "INFO: CHANGE_URL [${env.CHANGE_URL}]"
                    echo "INFO: SCM_URL [${scmURL}]"
                }
            }
        }
    }
    post {
        success {
            // Call feature build pipeline, pass env vars
            build job: "/${thisOrg}/Pipelines/orx-feature-build-pipeline", parameters: [
                [$class: "StringParameterValue", name: "BRANCH_NAME", value: env.BRANCH_NAME],
                [$class: "StringParameterValue", name: "BUILD_DISPLAY_NAME", value: env.BUILD_DISPLAY_NAME],
                [$class: "StringParameterValue", name: "BUILD_ID", value: env.BUILD_ID],
                [$class: "StringParameterValue", name: "BUILD_NUMBER", value: env.BUILD_NUMBER],
                [$class: "StringParameterValue", name: "BUILD_TAG", value: env.BUILD_TAG],
                [$class: "StringParameterValue", name: "BUILD_URL", value: env.BUILD_URL],
                [$class: "StringParameterValue", name: "CHANGE_AUTHOR", value: env.CHANGE_AUTHOR],
                [$class: "StringParameterValue", name: "CHANGE_AUTHOR_DISPLAY_NAME", value: env.CHANGE_AUTHOR_DISPLAY_NAME],
                [$class: "StringParameterValue", name: "CHANGE_AUTHOR_EMAIL", value: env.CHANGE_AUTHOR_EMAIL],
                [$class: "StringParameterValue", name: "CHANGE_ID", value: env.CHANGE_ID],
                [$class: "StringParameterValue", name: "CHANGE_TARGET", value: env.CHANGE_TARGET],
                [$class: "StringParameterValue", name: "CHANGE_TITLE", value: env.CHANGE_TITLE],
                [$class: "StringParameterValue", name: "CHANGE_URL", value: env.CHANGE_URL],
                [$class: "StringParameterValue", name: "SCM_URL", value: scmURL]
            ]
        }
    }
}
