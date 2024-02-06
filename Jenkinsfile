pipeline {
    agent any
    stages {
        stage('ST16269405p') {
            steps {
                echo "ST16269405p: Environement is prepared. Start to roll out to Test server"
            }
        }
          stage('ST26269405p') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                script {
                    sh """
                    puppet resource file /tmp/clone ensure=absent force=true
                    puppet resource file /tmp/clone ensure=directory
                    cd /tmp/clone
                    git clone https://github.com/RP23003387/POC_REPO.git
                    targets=testsvr6269405p.localdomain
                    locate_script='/tmp/clone/POC_REPO/script_to_run'
                    bolt script run \$locate_script -t \$targets -u raju -p raju --no-host-key-check --run-as root
                    echo "ST26269405p: TEST server is backup and update"
                    """
                }
            }
        }
      
        stage('ST36269405p') {
            steps {
                sh 'curl -Is http://testsvr6269405p.localdomain | head -n 1 > /tmp/TEST-result-file'
				echo 'ST6269405p: Test result for TEST server is generated: TEST-result-file'
            }
        }
        stage('ST46269405p') {
            steps {
                echo "ST46269405p: Test server's testig result has been inspected"
                    script{
                        v2 = input ( 
		                       			message: 'Action',
		                       			parameters: [choice(name:'',choices: ['Proceed Production', 'Rollback Test'])]
		                            	   )
                    }
               
            }
        }
        stage('ST56269405p') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                script {
                            if (v2 == 'Proceed Production') 
					{
                    echo 'ST56269405p: Proceed to Production Phase'
                    sh """
                    targets=prodsvr6269405p.localdomain
                    locate_script='/tmp/clone/POC_REPO/script_to_run'
                    bolt script run \$locate_script -t \$targets -u raju -p raju --no-host-key-check --run-as root
                    echo "Production container updated"
                    """
                }
                else
                        echo "ST56269405p: Reollback Test Server"
            }
        }
        }
        stage ('ST56269405p'){
            steps {
                echo 'ST56269405p: Completed updating to Production Container'
            }
        }
    }
}
