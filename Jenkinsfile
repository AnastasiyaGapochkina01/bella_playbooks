def checkPassed = true
pipeline {
  agent any
  parameters {
    choice(name: "playbook", choices: ["simple.yml", "prepare.yml", "test.yml"], description: "Плейбук")
    string(name: "host", defaultValue: "51.250.17.135", trim: true, description: "Хост для прокатки")
    gitParameter name: 'branch', type: 'PT_BRANCH', sortMode: 'DESCENDING_SMART', selectedValue: 'NONE', quickFilterEnabled: true
    booleanParam(name: "dryrun", defaultValue: true, description: "Тестовый запуск")
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
          export ANSIBLE_HOST_KEY_CHECKING=False
          ansible-playbook -i '$params.host,' $params.playbook --check
        """
      }
    }

    stage('Apply') {
      when not {
        expression { params.dryrun }
      }
      steps {
        sh """
          export ANSIBLE_HOST_KEY_CHECKING=False
          ansible-playbook -i '$params.host,' $params.playbook
        """
      }
    }
  }
}
