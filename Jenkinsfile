pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = 'bkup-test-image'
        PUPPET_SCRIPT_PATH = '/tmp/6269405p/work/6269405p_poc_repo/script_to_run_new'
        GIT_REPO_URL = 'https://github.com/RP23003387/POC_REPO.git'
        TARGET_USER = 'raju'
        TARGET_PASSWORD = 'raju'
        PRODUCTION_TARGET = 'prodsvr6269405p.localdomain'
        TEST_TARGET = 'testsvr6269405p.localdomain'
    }

    stages {
        stage('Prepare Environment') {
            steps {
                echo 'ST16269405p: Environment is prepared. Start to rollout to TEST server'
            }
        }

        stage('Backup and Update TEST Server') {
            steps {
                catchError(buildResult: 'SUCCESS') {
                    sh 'docker stop $DOCKER_IMAGE_NAME || true'
                    sh 'docker rm $DOCKER_IMAGE_NAME || true'
                    sh "docker commit TESTsvr6269405p $DOCKER_IMAGE_NAME"
                    sh '''
                        #!/bin/bash
                        puppet resource file /tmp/6269405p/work ensure=absent force=true;
                        puppet resource file /tmp/6269405p/work ensure=directory;
                        cd /tmp/6269405p/work;
                        git clone $GIT_REPO_URL
                        targets=$TEST_TARGET;
                        locate_script='$PUPPET_SCRIPT_PATH';
                        bolt script run $locate_script -t $targets -u $TARGET_USER -p $TARGET_PASSWORD --no-host-key-check --run-as root;
                    '''
                    echo 'ST26269405p: TEST server is backup and updated'
                }
            }
        }

        stage('Generate Test Result') {
            steps {
                sh 'curl -is http://testsvr6269405p.localdomain | head -n 1 > /tmp/TEST-result-file'
                echo 'ST36269405p: Test result for TEST server is generated: TEST-result-file'
            }
        }

        stage('Inspect Test Result') {
            steps {
                echo 'ST46269405p: Test server\'s testing result has been inspected'
                input 'Proceed Production or Rollback Test?'
            }
        }

        stage('Proceed to Production or Rollback Test') {
            steps {
                script {
                    if (input == 'Proceed Production') {
                        echo 'ST56269405p: Proceed to Production Phase'
                        catchError(buildResult: 'SUCCESS') {
                            sh '''
                                #!/bin/bash
                                puppet resource file /tmp/6269405p/work ensure=absent force=true;
                                puppet resource file /tmp/6269405p/work ensure=directory;
                                targets=$PRODUCTION_TARGET;
                                locate_script='$PUPPET_SCRIPT_PATH';
                                bolt script run $locate_script -t $targets -u $TARGET_USER -p $TARGET_PASSWORD --no-host-key-check --run-as root;
                            '''
                            echo 'ST66269405p: Production server is updated'
                        }
                    } else {
                        echo 'ST56269405p: Rollback Test server'
                        catchError(buildResult: 'SUCCESS') {
                            sh 'docker stop TESTsvr6269405p || true'
                            sh 'docker rm TESTsvr6269405p || true'
                            sh 'docker run --name TESTsvr6269405p -d $DOCKER_IMAGE_NAME'
                            echo 'ST66269405p: TEST server is rollback'
                        }
                    }
                }
            }
        }

        stage('Final Confirmation') {
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
