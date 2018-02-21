@Library('ctsrd-jenkins-scripts') _

properties([disableConcurrentBuilds(),
        disableResume(),
        [$class: 'GithubProjectProperty', displayName: '', projectUrlStr: 'https://github.com/CTSRD-CHERI/llvm/'],
        [$class: 'CopyArtifactPermissionProperty', projectNames: '*'],
        [$class: 'JobPropertyImpl', throttle: [count: 2, durationName: 'hour', userBoost: true]],
        pipelineTriggers([githubPush()])
])



scmInfo = null

def doBuild() {
    stage("Do build") {
        scmInfo = checkout scm
        setGitHubStatus(scmInfo, [message: 'Build pending...'])
        sleep(60)
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
        echo("SCM INFO: ${scmInfo}")
        if (!scmInfo)
            error("FAILED!")
        setGitHubStatus(scmInfo, [context: "jenkins/pending", message: 'this is still pending..', result: 'PENDING'])
        setGitHubStatus(scmInfo, [context: "jenkins/success", message: 'Done!' result: 'SUCCESS'])
        setGitHubStatus(scmInfo, [context: "jenkins/error", message: 'Failed for some reason', result: 'ERROR'])
        setGitHubStatus(scmInfo, [context: "jenkins/failure", message: '15 unit tests failed', result: 'FAILURE'])
        setGitHubStatus(scmInfo, [context: "jenkins/current", message: 'default result'])
        // finally set the default status
        currentBuild.result = currentBuild.currentResult
        setGitHubStatus(scmInfo, [message: 'Build completed'])
    }
}
