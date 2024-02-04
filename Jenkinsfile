pipeline {
    agent any
    environment {
        DOCKER_IMAGE_NAME = 'bkup-test-image'
        PUPPET_SCRIPT_PATH = '/tmp/6269405p/work/POC_REPO/script_to_run_new'
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
                    script {
                        deleteDir() // Clean workspace
                        sh 'docker stop $DOCKER_IMAGE_NAME || true'
                        sh 'docker rm $DOCKER_IMAGE_NAME || true'
                        sh "docker commit TESTsvr6269405p $DOCKER_IMAGE_NAME"
                        sh '''
                            #!/bin/bash
                            puppet resource file /tmp/6269405p/work ensure=absent force=true;
                            puppet resource file /tmp/6269405p/work ensure=directory;
                            cd /tmp/6269405p/work;
                            git clone $GIT_REPO_URL POC_REPO
                            targets=$TEST_TARGET;
                            locate_script='$PUPPET_SCRIPT_PATH';  // Update this path
                            bolt script run $locate_script -t $targets -u $TARGET_USER -p $TARGET_PASSWORD --no-host-key-check --run-as root;
                        '''
                        echo 'ST26269405p: TEST server is backup and updated'
                    }
                }
            }
        }

        // Remaining stages...

    }
}
