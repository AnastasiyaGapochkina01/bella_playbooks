pipeline {
  agent any
  parameters {
    booleanParam(name: "dryrun", defaultValue: true, description: "Тестовый запуск")
  }

  stages {
    stage('DryRun') {
      when {
        expression { params.dryrun }
      }
      steps {
        echo "THIS IS DRYRUN!"
      }
    }

    stage('Apply') {
      steps {
        echo "THIS IS APPLY!"
      }
    }
  }

}
