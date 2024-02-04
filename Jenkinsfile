pipeline {
    agent any

    stages {
        stage('56269405p') {
            steps {
                echo 'ST156269405p: Environment is prepared. Start to rollout to TEST server'
            }
        }

        stage('ST256269405p') {
            steps {
                sh 'docker stop bkup-test-image || true'
                sh 'docker rm bkup-test-image || true'
                sh 'docker commit TESTsvr6269405p bkup-test-image'
                sh '''
                    #!/bin/bash
                    puppet resource file /tmp/6269405p/work ensure=absent force=true;
                    puppet resource file /tmp/6269405p/work ensure=directory;
                    cd /tmp/56269405p/work;
                    git clone https://github.com/RP23003387/POC_REPO.git
                    targets=TESTsvr6269405p;
                    locate_script='/tmp/6269405p/work/POC_REPO/script_to_run_new';
                    bolt script run $locate_script -t $targets -u raju -p raju --no-host-key-check --run-as root;
                '''

                echo 'ST26269405p: TEST server is backup and updated'
            }
        }

        stage('ST36269405p') {
            steps {
                sh 'curl -is http://TESTsvr6269405p.localdomain | head -n 1 > /tmp/TEST-result-file'
                echo 'ST36269405p: Test result for TEST server is generated: TEST-result-file'
            }
        }

        stage('ST46269405p') {
            steps {
                echo 'ST46269405p: Test server\'s testing result has been inspected'
                input 'Proceed Production or Rollback Test?'
            }
        }

        stage('ST56269405p') {
            steps {
                script {
                    if (input == 'Proceed Production') {
                        echo 'ST56269405p: Proceed to Production Phase'
                        sh '''
                            #!/bin/bash
                            puppet resource file /tmp/6269405p/work ensure=absent force=true;
                            puppet resource file /tmp/6269405p/work ensure=directory;
                            targets=PRODsvr6269405p;
                            locate_script='/tmp/6269405p/work/POC_REPO/script_to_run_new';
                            bolt script run $locate_script -t $targets -u raju -p raju --no-host-key-check --run-as root;
                        '''
                        echo 'ST6269405p: Production server is updated'
                    } else {
                        echo 'ST56269405p: Rollback Test server'
                        sh 'docker stop TESTsvr6269405p || true'
                        sh 'docker rm TESTsvr6269405p || true'
                        sh 'docker run --name TESTsvr6269405p -d bkup-test-image'
                        echo 'ST56269405p: TEST server is rollback'
                    }
                }
            }
        }

        stage('ST66269405p') {
            steps {
                script {
                    if (input == 'Proceed Production') {
                        echo 'ST66269405p: Production server is updated'
                    } else {
                        echo 'ST66269405p: TEST server is rollback'
                    }
                }
            }
        }
    }
}
