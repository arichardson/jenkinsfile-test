
node {
  stage('checkout') {
    def scmStr = scm.properties.collect{it}.join('\n')
    echo("SCM = ${scmStr}")
    def result = checkout scm
    echo("result = ${result}")
  }
}
