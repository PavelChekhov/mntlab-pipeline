node (env.MASTER) {
	stage('Preparation') {
		git url:'https://github.com/PavelChekhov/mntlab-pipeline.git', branch: 'pchekhov'
	}
    stage('Gradle Build') {
        sh "/opt/gradle/gradle-4.3/bin/gradle build"
    }
    stage ('Testing') {
    	parallel (
    		cucumber: {
    			stage ('cucumber') {
    				sh "/opt/gradle/gradle-4.3/bin/gradle cucumber"
    			}
    		},
    		jacoco: {
    			stage ('jacoco') {
    				sh "/opt/gradle/gradle-4.3/bin/gradle jacocoTestReport"
    			}
    		},
    		unit: {
    			stage ('unit test') {
    				sh "/opt/gradle/gradle-4.3/bin/gradle test"
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
stage('Packaging and Publishing results') {
	sh "tar -xzf pchekhov_dsl_script.tar.gz jobs.groovy"
	sh "tar -czf pipeline-pchekhov-${BUILD_NUMBER}.tar.gz jobs.groovy Jenkinsfile"
	archiveArtifacts artifacts: 'pipeline-pchekhov-${BUILD_NUMBER}.tar.gz'
//	nexusArtifactUploader artifacts: 
//[[artifactId: "${BUILD_NUMBER}",classifier: 'tar.gz',file: 'pipeline-pchekhov-"${BUILD_NUMBER}".tar.gz',type: "${BUILD_NUMBER}"]], credentialsId: 'admin', groupId: 'groupid', nexusUrl: '127.0.0.1:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-releases', version: 'release'
	sh "curl -v --user 'admin:admin' --upload-file ./pipeline-pchekhov-${BUILD_NUMBER}.tar.gz http://127.0.0.1:8081/#browse/browse:maven-releases/pipeline-pchekhov-${BUILD_NUMBER}.tar.gz"
	}
stage('Asking for manual approval') {
timeout(time: 120, unit: 'SECONDS') {
        input message: 'Do you want to release this build?', ok: 'Yes' 
    }
}
stage('Deployment') {
	sh "java -jar build/libs/gradle-simple.jar"
	} 
    stage ('Sending status') {
    	echo "SUCCESS"
    }
}
