@Library('ctsrd-jenkins-scripts') _

properties([disableConcurrentBuilds(),
        disableResume(),
        [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/CTSRD-CHERI/llvm/'],
        [$class: 'CopyArtifactPermissionProperty', projectNames: '*'],
        [$class: 'JobPropertyImpl', throttle: [count: 2, durationName: 'hour', userBoost: true]],
        pipelineTriggers([githubPush()])
])

def doBuild() {
    def scmInfo = null
    stage("Do build") {
        scmInfo = checkout scm
        setGitHubStatus(scmInfo)
        sleep(10)
    }
}

node('linux') {
    try {
        env.SDKROOT_DIR = "${env.WORKSPACE}/sdk"
        doBuild()
        // Scan for compiler warnings
        warnings canComputeNew: false, canResolveRelativePaths: true, consoleParsers: [[parserName: 'Clang (LLVM based)']]
        step([$class: 'AnalysisPublisher', canComputeNew: false])
    } finally {
        dir(env.SDKROOT_DIR) {
            deleteDir()
        }
        setGitHubStatus(scmInfo, [context: "jenkins/pending", result: 'PENDING'])
        setGitHubStatus(scmInfo, [context: "jenkins/success", result: 'SUCCESS'])
        setGitHubStatus(scmInfo, [context: "jenkins/error", result: 'ERROR'])
        setGitHubStatus(scmInfo, [context: "jenkins/failure", result: 'FAILURE'])
        setGitHubStatus(scmInfo, [context: "jenkins/current", result: null])
    }
}
