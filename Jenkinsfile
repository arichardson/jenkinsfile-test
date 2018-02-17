
def printVar(k, v) {
  def valueStr = v.properties.collect{it}.join('\n\t')
  echo("Value of ${k} = \n\t${valueStr}")
}

this.binding.variables.each {k,v -> printVar(k, v)}


node {
  stage('checkout') {
    // def scmStr = scm.properties.collect{it}.join('\n')
    printVar("scm", scm)
    

    def result = checkout scm
    echo("result = ${result}")
  }
}
