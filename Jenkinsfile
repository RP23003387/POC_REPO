pipeline 
{
	agent any
      	stages 
	{
		stage('ST16269405p') 
	  	{
          		steps 
			{
            			echo 'ST16269405p: Environment is prepared. Start to rollout to TEST server'
          		}
          	}
		stage('ST26269405p') 
		{
	          	steps 
			{
				sh '''#!/bin/bash
				docker rm -f bkup-test-image 
				docker commit TESTsvr6269405p bkup-test-image
				puppet resource file /tmp/6269405p/work ensure=absent force=true;
				puppet resource file /tmp/6269405p/work ensure=directory;
				cd /tmp/6269405p/work;
				git clone https://github.com/RP23003387/POC_REPO.git;
				targets=TESTsvr6269405p;
				locate_script='/tmp/6269405p/work/POC_REPO/script_to_run_final';
				bolt script run \$locate_script -t \$targets -u raju -p raju --no-host-key-check --run-as root;
				'''
				echo 'ST26269405p: TEST server is backup and updated'
	          	}
          	}
          	stage('ST36269405p') 
		{
	          	steps 
			{
				sh 'curl -Is http://testsvr6269405p.localdomain | head -n 1 > /tmp/TEST-result-file'
				echo 'ST6269405p: Test result for TEST server is generated: TEST-result-file'
	          	}
          	}
          	stage('ST6269405p') 
		{
	          	steps 
			{
				echo 'ST6269405p: TEST server’s testing result has been inspected'

		                script 
				{
		                	v2 = input ( 
		                       			message: 'Action',
		                       			parameters: [choice(name:'',choices: ['Proceed Production', 'Rollback Test'])]
		                            	   )
		                }
			}
          	}
          	stage('ST56269405p') 
		{
	          	steps 
			{
		                script 
				{
		                   	if (v2 == 'Proceed Production') 
					{
						echo 'ST56269405p: Proceed to Production Phase'
		
						sh '''#!/bin/bash
						targets=PRODsvr6269405p;
						locate_script='/tmp/6269405p/work/POC_REPO/script_to_run_final';
						bolt script run \$locate_script -t \$targets -u raju -p raju --no-host-key-check --run-as root;
						'''
		                   	} 
					else
					{
		  	            		echo "ST56269405p: Rollback Test server"

						sh '''#!/bin/bash
						docker rmi bkup-test-image
						docker load -i /tmp/BKUP-TEST-image.tar
						'''
		                   	}		      
		                }  
	          	}
          	}
		stage('ST6_3248914N') 
		{
          		steps 
			{
		               script 
				{
		                  	if (v2 == 'Proceed Production') 
					{
						echo 'ST66269405p: Production server is updated'
		                   	} 
					else
					{
		  	            		echo "ST66269405p: TEST server is rollback"
		                   	}		      
		                }  
          		}
          	}
	}
}
