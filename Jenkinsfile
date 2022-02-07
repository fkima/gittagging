#!groovy

pipeline {
    agent any
    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout([$class: 'GitSCM', branches: [[name: "*/${env.BRANCH_NAME}"]], extensions: [], userRemoteConfigs: [[credentialsId: 'GitHub', url: 'https://github.com/kimachinskiy/gittagging.git']]])
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
    String pathPranchName = 'fix'

    String gitLastTaggedCommitHash = gitLastTaggedCommitHash()
    String gitLastCommitHash = gitLastCommitHash()
    String gitCommitTag = gitCommitTag(gitLastTaggedCommitHash)
    List<Integer> tags = parsegitCommitTag(gitCommitTagDelimeter, gitCommitTag)
    List<Integer> gitCommitTags = gitCommitTagIncrease(tags, majorBranchName, minorBranchName, pathPranchName )
    gitCommitTag = gitCommitTags.join(gitCommitTagDelimeter as String)
    addGitCommitUser(emailGitUser, nameGitUser)
    gitAddTag(gitCommitTag, gitLastCommitHash)
    gitPushTag(gitCommitTag)
}

//Get git last tagget commit hash
String gitLastTaggedCommitHash() {
    result = ''
    try {
        result = sh(script: 'git rev-list --tags --max-count=1', returnStdout: true).trim()
    } catch (Exception ex) {
        println(ex.getMessage())
        result = ''
    }
    println("gitLastTaggedCommitHash - ${result}")
    return result
}

//Get git latest commit hash
String gitLastCommitHash() {
    return sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
}

//Get git TAG for commit
String gitCommitTag(String gitCommitTaggedCommitHash) {
    String result = ''
    if (gitCommitTaggedCommitHash.length() == 40) {
        result = sh(script: "git describe --tags ${gitCommitTaggedCommitHash}", returnStdout: true).trim()
    } else {
        result = '0.0.0'
    }
    return result
}

//Parse git TAG for delimeter
List<Integer> parsegitCommitTag(char gitCommitTagDelimeter, String tag) {
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
    String pathPranchName) {
    if (BRANCH_NAME == majorBranchName) {
        tags[0] += 1
        tags[1] = 0
        tags[2] = 0
    } else if (BRANCH_NAME == minorBranchName) {
        tags[1] += 1
        tags[2] = 0
    } else if (BRANCH_NAME == pathPranchName) {
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

//Get git log
void getGitLog() {
    sh(script: 'git log', returnStdout: true).trim()
}

//Get remote repository URL
String getGitRemoteRepositoryUrl() {
    return sh(script: 'git config --get remote.origin.url', returnStdout: true).trim()
}

