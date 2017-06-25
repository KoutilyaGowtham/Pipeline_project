// Define some globals here, for convenience
def thisOrg = "Beethoven"
def mavenBuildOpts = "-Dmaven.wagon.http.ssl.insecure=true -Dmaven.wagon.http.ssl.allowall=true -Dmaven.wagon.http.ssl.ignore.validity.dates=true"
def mavenSonarOpts = "-Dsonar.branch=dev"
def featureBranchPrefix = "ORCH-"
def pullRequestPrefix = "pull request"
// https://docs.sonarqube.org/display/SCAN/Analyzing+with+SonarQube+Scanner+for+Maven
def sonarScannerForMavenVersion = "3.2"
// https://mvnrepository.com/artifact/org.jacoco/jacoco-maven-plugin
def jaCoCoVersion = "0.7.9"

// Build state
def isPR = false
def isPRtoDev = false
def isMergePRtoDev = false

// To be determined from pom file
def artifactName = ""
def artifactVersion = ""

// To be read in from properties file
def artifactShortname = ""
def deploymentType = ""
def tierType = ""
def ownerContact = ""

echo "INFO: BRANCH_NAME [${env.BRANCH_NAME}] BUILD_DISPLAY_NAME [${env.BUILD_DISPLAY_NAME}] BUILD_ID [${env.BUILD_ID}] BUILD_NUMBER [${env.BUILD_NUMBER}]"
echo "INFO: BUILD_TAG [${env.BUILD_TAG}]"
echo "INFO: BUILD_URL [${env.BUILD_URL}]"
echo "INFO: SCM_URL [${env.SCM_URL}]"

// Get a temporary artifact name so we have something to put in build display name
def matcher
matcher = (env.SCM_URL =~ ~/^.*\/(.*)\.git$/)
if (matcher.find()) {
    artifactName = matcher[0][1]
    currentBuild.displayName = "#${currentBuild.number}_${artifactName}_${env.BRANCH_NAME}"
}
artifactName = ""
// matcher is not serializable, discard to make CPS happy
matcher = null


// All the CHANGE_ env vars are only defined for a PR
if (env.CHANGE_ID == null || env.CHANGE_ID == "") {
    echo "INFO: this is not a PR"
} else {
    isPR = true
    echo "INFO: CHANGE_AUTHOR [${env.CHANGE_AUTHOR}] CHANGE_AUTHOR_DISPLAY_NAME [${env.CHANGE_AUTHOR_DISPLAY_NAME}]"
    echo "INFO: CHANGE_AUTHOR_EMAIL [${env.CHANGE_AUTHOR_EMAIL}]"
    echo "INFO: CHANGE_ID [${env.CHANGE_ID}] CHANGE_TARGET [${env.CHANGE_TARGET}]"
    echo "INFO: CHANGE_TITLE [${env.CHANGE_TITLE}]"
    echo "INFO: CHANGE_URL [${env.CHANGE_URL}]"
}

stage("Determine Calling State") {
    node("${thisOrg}-build") {
        
        def thisBranch
        def headBranch
        def baseBranch
        def isPRMerge = false
        def isFromFeature = false
        // GitHub is consistent with her commit strings, fortunately, so let's use those
        def matchStringGitHubMergeBranch = ~/Merge branch '(.+)' into (.+)$/
        def matchStringGitHubMergePR = ~/Merge (.+) from (.+)$/

        // Empty workspace
        deleteDir()

        git url: env.SCM_URL

        if (isPR == true) {
            thisBranch = "pull/${env.CHANGE_ID}/head:${env.BRANCH_NAME}"
        } else {
            thisBranch = "refs/heads/${env.BRANCH_NAME}"
        }

        // Fetch this branch and switch to it
        sh "git fetch origin ${thisBranch}"
        sh "git checkout ${env.BRANCH_NAME}"

        def commitString = sh(script: "git log --pretty='%s' | head -1", returnStdout: true).trim()
        echo "INFO: Last git commit string for this branch [${commitString}]"
        
        // See if this a merge of a GitHub PR
        matcher = (commitString =~ matchStringGitHubMergePR)
        // Currently Pipeline does not support multiple assignments, so we do this the hard way
        if (matcher.find()) {
            headBranch = matcher[0][1]
            baseBranch = matcher[0][2]
            echo "INFO: this is a merge of a PR [${headBranch}] from branch [${baseBranch}]"
        } else {
            // See if this is a merge of a GitHub branch
            matcher = (commitString =~ matchStringGitHubMergeBranch)
            // Currently Pipeline does not support multiple assignments, so we do this the hard way
            if (matcher.find()) {
                headBranch = matcher[0][1]
                baseBranch = matcher[0][2]
                echo "INFO: This is a merge from Head branch [${headBranch}] into Base branch [${baseBranch}]"
            }
        }
