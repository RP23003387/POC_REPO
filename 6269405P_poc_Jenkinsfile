pipeline {
    agent any
    stages {
        stage('ST16269405P') {
            steps {
                echo 'Continue from previous phase: Ready to deploy next environment.'
            }
        }
        stage('ST26269405P') {
            steps {
                input('Do you want to update to UAT container?')
            }
        }
        stage('ST36269405P') {
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
                    locate_script='/tmp/clone/devops_repo/script_to_run'
                    bolt script run \$locate_script -t \$targets -u clientadm -p user123 --no-host-key-check --run-as root
                    echo "UAT container updated"
                    """
                }
            }
        }
        stage('ST46269405P') {
            steps {
                input('Do you want to deploy to Production container?')
            }
        }
        stage('ST56269405P') {
            when {
                expression { currentBuild.resultIsBetterOrEqualTo('SUCCESS') }
            }
            steps {
                script {
                    sh """
                    targets=prodsvr626940p.local
                    locate_script='/tmp/clone/devops_repo/script_to_run'
                    bolt script run \$locate_script -t \$targets -u clientadm -p user123 --no-host-key-check --run-as root
                    echo "Production container updated"
                    """
                }
            }
        }
        stage('Completed updating Operation') {
            steps {
                echo 'Completed updating to Production Container'
            }
        }
    }
}
