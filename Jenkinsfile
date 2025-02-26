pipeline {
  agent any
  environment {
    DIR = "/var/lib/jenkins/ansible"
  }
  parameters {
    booleanParam(name: "dryrun", defaultValue: true, description: "Тестовый запуск")
    gitParameter name: 'branch', type: 'PT_BRANCH', sortMode: 'DESCENDING_SMART', selectedValue: 'NONE', quickFilterEnabled: true
    string(name: "host", defaultValue: "simple.yml", trim: true, description: "Хост для прокатки")
    choice(name: "playbook", choices: ["simple.yml", "prepare.yml", "test.yml"], description: "Плейбук")
  }

  stages {
    stage('Checkout repo') {
      steps {
        checkout([$class: 'GitSCM', branches: [[name: "${branch}"]], doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], userRemoteConfigs: [[credentialsId: 'jenkins_ssh_key', url: "$git_url"]]])
      }
    }
    stage('DryRun') {
      when {
        expression { params.dryrun }
      }
      steps {
        sh """
          ansible-playbook -i '{ params.host },' { params.playbook } --check
        """
      }
    }

    stage('Apply') {
      steps {
        echo "THIS IS APPLY!"
      }
    }
  }

}
