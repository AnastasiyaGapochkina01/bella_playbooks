def checkPassed = true
def cmd = ['/bin/bash', '-c', "git clone --single-branch https://github.com/AnastasiyaGapochkina01/bella_playbooks.git > /dev/null 2>&1 ; cd bella_playbooks ; git ls-tree -r origin/main  --name-only"]
def file_str = cmd.execute().text
def files = file_str.split('\n').collect { it }

pipeline {
  agent any
  parameters {
    choice(name: "playbook", choices: files, description: "Плейбук")
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
      when {
        expression { params.dryrun == "false" }
        
      }
      input {
         message "Should we continue play?"
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
