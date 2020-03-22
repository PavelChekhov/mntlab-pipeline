node (env.MASTER) {
	stage('Preparation') {
		git url:'https://github.com/PavelChekhov/mntlab-pipeline.git', branch: 'pchekhov'
	}
    stage('Gradle Build') {
        sh "/opt/gradle/gradle-6.2.2/bin/gradle build"
    }
    stage ('Testing') {
    	parallel (
    		cucumber: {
    			stage ('cucumber') {
    				sh "/opt/gradle/gradle-6.2.2/bin/gradle cucumber"
    			}
    		},
    		jacoco: {
    			stage ('jacoco') {
    				sh "/opt/gradle/gradle-6.2.2/bin/gradle jacocoTestReport"
    			}
    		},
    		unit: {
    			stage ('unit test') {
    				sh "/opt/gradle/gradle-6.2.2/bin/gradle test"
    			}
    		}
    	)
    }
    stage ('Triggering job and fetching artefact after finishing') {
    	build job: 'MNTLAB-pchekhov-child1-build-job', 
    		  parameters: [string(name: 'BRANCH_NAME', value: "pchekhov")],
    		  wait: true
    	step([$class: 'CopyArtifact',
	          projectName: 'MNTLAB-pchekhov-child1-build-job',
        	  filter: 'pchekhov_dsl_script.tar.gz']) 
    		
    }
    stage ('Sending status') {
    	echo "SUCCESS"
    }
}
