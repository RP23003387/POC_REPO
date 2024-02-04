pipeline {
    agent any

    environment {
        PUPPET_SCRIPT_PATH = '/tmp/6269405p/work/script_to_run_new.sh'  // Update with the correct path
        TARGET_USER = 'raju'
        TARGET_PASSWORD = 'raju'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                script {
                    checkout scm
                }
            }
        }

        stage('Prepare Environment') {
            steps {
                echo 'ST16269405p: Environment is prepared. Start to rollout to TEST server'
            }
        }

        stage('Backup and Update TEST Server') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'FAILURE') {
                    deleteDir()
                    sh '''
                        docker stop bkup-test-image
                        docker rm bkup-test-image
                        docker commit TESTsvr6269405p bkup-test-image
                        puppet resource file /tmp/6269405p/work ensure=absent force=true
                        puppet resource file /tmp/6269405p/work ensure=directory
                        cd /tmp/6269405p/work
                        git clone https://github.com/RP23003387/POC_REPO.git POC_REPO
                        targets=testsvr6269405p.localdomain
                        locate_script=$PUPPET_SCRIPT_PATH
                        chmod +x $locate_script  # Make the script executable
                        $locate_script -t $targets -u $TARGET_USER -p $TARGET_PASSWORD --no-host-key-check --run-as root
                    '''
                }
            }
        }

        stage('Generate Test Result') {
            steps {
                sh '''
                    head -n 1
                    curl -is http://testsvr6269405p.localdomain
                    echo 'ST36269405p: Test result for TEST server is generated: TEST-result-file'
                '''
            }
        }

        stage('Inspect Test Result') {
            steps {
                echo 'ST46269405p: Test server\'s testing result has been inspected'
            }
        }

        stage('Proceed to Production or Rollback Test') {
            steps {
                script {
                    input message: 'Proceed Production or Rollback Test?', ok: 'Proceed or Abort', submitter: 'jadmin'
                }
            }
        }

        stage('Final Confirmation') {
            steps {
                echo 'Final Confirmation stage'
                // Add your final confirmation steps here
            }
        }
    }

    post {
        always {
            echo 'Pipeline completed'
        }
    }
}
