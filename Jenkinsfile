#!groovy

pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                script {
                    String repositoryURL = sh(script: 'git config --get remote.origin.url', returnStdout: true).trim()
                    checkout([$class: 'GitSCM', \
                    branches: [[name: "*/${env.BRANCH_NAME}"]], \
                    extensions: [], \
                    userRemoteConfigs: [[credentialsId: 'GitHub', url: "${repositoryURL}"]]])
                }
            }
        }
        stage('New path version12!') {
            steps {
                script {
                    gitTagging()
                }
            }
        }
    }
}

// Main function for increase Git TAG. For example: 1.0.0 -> 1.1.0 -> 1.1.1 -> 1.2.0 -> 2.0.0 ->
void gitTagging() {
    String emailGitUser = 'user@useremailexample.com'
    String nameGitUser = 'User'
    char gitCommitTagDelimeter = '.'
    String majorBranchName = 'main'
    String minorBranchName = 'develop'
    String pathBranchName = 'fix'

    String gitLastCommitHash = gitLastCommitHash()

    if (!isCommitTagged(gitLastCommitHash)) {
        String gitCommitTag = gitLastTag()
        List<Integer> tags = parseGitCommitTag(gitCommitTagDelimeter, gitCommitTag)
        List<Integer> gitCommitTags = gitCommitTagIncrease(tags, majorBranchName, minorBranchName, pathBranchName )
        gitCommitTag = gitCommitTags.join(gitCommitTagDelimeter as String)
        addGitCommitUser(emailGitUser, nameGitUser)
        gitAddTag(gitCommitTag, gitLastCommitHash)
        gitPushTag(gitCommitTag)
    }
}

//Get git latest commit hash
String gitLastCommitHash() {
    return sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
}

//Checks a commit for a tag
boolean isCommitTagged(String commitHash) {
    String result = sh(script: "git tag --points-at ${commitHash}", returnStdout: true).trim()
    boolean isTagged = result ? true : false
    return isTagged
}

// Get last TAG in current branch
String gitLastTag() {
    String result
    try {
        result = sh(script: 'git describe --abbrev=0', returnStdout: true).trim()
    } 
    catch (Exception ex) {
        result = '0.0.0'
    }
    return result
}

//Parse git TAG for delimeter
List<Integer> parseGitCommitTag(char gitCommitTagDelimeter, String tag) {
    List<String> tags = tag.tokenize(gitCommitTagDelimeter)
    List<Integer> intTags = []
    for (item in tags) {
        intTags.add(Integer.parseInt(item))
    }
    return intTags
}

//Add user for commited TAG
void addGitCommitUser(String emailGitUser, String nameGitUser) {
    sh(script: "git config user.email ${emailGitUser}", returnStdout: true).trim()
    sh(script: "git config user.name ${nameGitUser}", returnStdout: true).trim()
}

//Increments the TAG depending on the branch
List<Integer> gitCommitTagIncrease(
    List<Integer> tags,
    String majorBranchName,
    String minorBranchName,
    String pathBranchName) {
    if (BRANCH_NAME == majorBranchName) {
        tags[0] += 1
        tags[1] = 0
        tags[2] = 0
    } else if (BRANCH_NAME == minorBranchName) {
        tags[1] += 1
        tags[2] = 0
    } else if (BRANCH_NAME == pathBranchName) {
        tags[2] += 1
    }
    return tags
    }

// Add git TAG to local repository
void gitAddTag(String gitCommitTag, String gitLastCommitHash) {
    sh(script: "git tag -a ${gitCommitTag} -m 'Add TAG ${gitCommitTag}' ${gitLastCommitHash}", \
    returnStdout: true).trim()
}

// Push git TAG to remote repository
void gitPushTag(String gitCommitTag) {
    sh(script: "git push origin ${gitCommitTag}", returnStdout: true).trim()
}
