
node {
  stage("Build) {
    def scmStr = scm.properties.collect{it}.join('\n')
    echo("SCM = ${scmStr}")
  }
}
