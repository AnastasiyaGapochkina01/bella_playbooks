pipeline {
  agent any
  environment {
    DIR = "/var/lib/jenkins/ansible"
  }
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
    
    boolean checkPassed = true
    stage('DryRun') {
      when {
        expression { params.dryrun }
      }
      steps {
        try{
          sh """
            export ANSIBLE_HOST_KEY_CHECKING=False
            ansible-playbook -i '$params.host,' $params.playbook --check
          """
        }catch (Exception e){
          checkPassed = false
        }
        
      }
    }

    stage('Apply') {
      if (checkPassed) {
        steps {
          sh """
            export ANSIBLE_HOST_KEY_CHECKING=False
            ansible-playbook -i '$params.host,' $params.playbook
          """
        }
      }
    }
  }

}
