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
                    docker rm bkup-test-image || true
                    docker commit TESTsvr6269405p bkup-test-image
                    puppet resource file /tmp/6269405p/work ensure=absent force=true
                    puppet resource file /tmp/6269405p/work ensure=directory
                    cd /tmp/6269405p/work
                    git clone https://github.com/RP23003387/POC_REPO.git
                    targets=testsvr6269405p.localdomain
                    locate_script='/tmp/6269405p/work/POC_REPO/6269405p_script'
                    bolt script run \$locate_script -t \$targets -u raju -p raju --no-host-key-check --run-as root
                    echo "ST26269405p: TEST server is backup and update"
                    """
                }
            }
        }
      
        stage('ST36269405p') {
            steps {
                sh 'curl -Is http://testsvr6269405p.localdomain | head -n 1 > /tmp/TEST-result-file.txt'
				echo 'ST6269405p: Test result for TEST server is generated: TEST-result-file'
            }
        }
        stage('ST46269405p') {
            steps {
                echo "ST46269405p: Test server's testing result has been inspected"
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
                    locate_script='/tmp/6269405p/work/POC_REPO/6269405p_script'
                    bolt script run \$locate_script -t \$targets -u raju -p raju --no-host-key-check --run-as root
                    echo "Production container updated"
                    """
                }
                else 
                {
                        echo "ST56269405p: Rollback Test Server"
                    sh """
                    docker stop TESTsvr6269405p
                    docker rm TESTsvr6269405p
                    docker run -d --privileged --network customnetwork -it -h TESTsvr6269405p.localdomain --name TESTsvr6269405p --add-host=TESTsvr6269405p.localdomain:172.20.113.60 --ip=192.168.20.100 bkup-test-image tail -f /dev/null
                    service ssh start
                    service apache2 start

                    """  
        }
                }
        }
        }
        stage('ST66269405p'){
            steps {
                echo 'ST66269405p: Completed updating to Production Container'
            }
        }
    }
}
