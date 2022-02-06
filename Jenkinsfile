#!groovy

pipeline {
    agent any
    stages {
        // stage('Checkout') {
        //     steps {
        //         script {
        //             checkout([$class: 'GitSCM', branches: [[name: "*/${env.BRANCH_NAME}"]], extensions: [], userRemoteConfigs: [[credentialsId: 'GitHub', url: 'https://github.com/kimachinskiy/gittagging.git']]])
        //         }
        //     }
        // }
        stage('New major version!') {
            steps {
                script {
                    gitTagging()
                }
            }
        }
    }
}

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

String gitLastTaggedCommitHash() {
    result = ''
    try {
        result = sh(script: 'git rev-list --tags --max-count=1', returnStdout: true).trim()
    } catch (Exception ex) {
        result = ''
    }
    println("gitLastTaggedCommitHash - ${result}")
    return result
}

String gitLastCommitHash() {
    return sh(script: 'git rev-parse HEAD', returnStdout: true).trim()
}
String gitCommitTag(String gitCommitTaggedCommitHash) {
    String result = ''
    if (gitCommitTaggedCommitHash.length() == 40) {
        result = sh(script: "git describe --tags ${gitCommitTaggedCommitHash}", returnStdout: true).trim()
    } else {
        result = '0.0.0'
    }
    return result
}

List<Integer> parsegitCommitTag(char gitCommitTagDelimeter, String tag) {
    List<String> tags = tag.tokenize(gitCommitTagDelimeter)
    List<Integer> intTags = []
    for (item in tags) {
        intTags.add(Integer.parseInt(item))
    }
    return intTags
}

void addGitCommitUser(String emailGitUser, String nameGitUser) {
    sh(script: "git config user.email ${emailGitUser}", returnStdout: true).trim()
    sh(script: "git config user.name ${nameGitUser}", returnStdout: true).trim()
}

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

void gitAddTag(String gitCommitTag, String gitLastCommitHash) {
    sh(script: "git tag -a ${gitCommitTag} -m 'Add TAG ${gitCommitTag}' ${gitLastCommitHash}", \
    returnStdout: true).trim()
}

void gitPushTag(String gitCommitTag) {
    sh(script: "git push origin ${gitCommitTag}", returnStdout: true).trim()
}

void getGitLog() {
    sh(script: 'git log', returnStdout: true).trim()
}

String getGitRemoteRepositoryUrl() {
    return sh(script: 'git config --get remote.origin.url', returnStdout: true).trim()
}

