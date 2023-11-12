pipeline{
  agent any
  stages{
    stage("Clone Resource"){
      steps{
        sh """
          ansible-playbook -i ansible/inventory.ini -l workers ansible/git.yml
        """
      }
    }
    stage("Build Project"){
      steps{
        sh """
          ansible-playbook -i ansible/inventory.ini -l workers ansible/build.yml
        """
      }
    }
    stage("Docker Stage"){
      steps{
        withCredentials([usernamePassword(credentialsId: 'nexus-id', passwordVariable: 'REGISTRY_PASS', usernameVariable: 'REGISTRY_NAME')]) {
           sh """
            ansible-playbook -i ansible/inventory.ini -l workers \
            -e registry_name=${REGISTRY_NAME} \
            -e registry_pass=${REGISTRY_PASS} \
            ansible/docker.yml
          """
        }
      }
    }

    stage("Deploy Apps"){
      steps{
        sh """
          ansible-playbook -i ansible/inventory.ini -l workers ansible/deploy.yml
        """
      }
    }
  }
}
