/*=============================================================
IBM Confidential                                                  
   
OCO Source Materials                                              
   
Copyright IBM Corp. 2021		                                     
     
The source code for this program is not published or otherwise    
divested of its trade secrets, irrespective of what has been      
deposited with the U.S. Copyright Office.                         
                                                                  
=============================================================*/

//Global variables
def IMAGE_TAG = ""
def IMAGE_NAME = "che-plugin-registry"

// --------------------------------------------------------------------------------------------------------------------------------
// Pipeline Starts here
// --------------------------------------------------------------------------------------------------------------------------------

node {
    checkout scm
	try {
		
		stage('Load build library and setup environment') {
            echo "==== LOADING THE COMMON BUILD LIBRARIES ===="
            @Library('api-hub-build-lib') _
		    def command = "date -u +%Y%m%d-%H%M%S"
            def currenttimestamp = callAPI command
			echo "${currenttimestamp}"
			IMAGE_TAG = "${currenttimestamp}"
		    echo "${IMAGE_TAG}"
		    echo "Stage completed"
		}//closing stage
			
		
		stage('Pull the sources from github.ibm.com/ibm-api-marketplace') {
	    	echo "===== Checking the che version deployed on cluster ===="
	    	withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
	    		sh "git clone https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-resources.git"
				sh '''#!/bin/bash
				CHE_IMAGE=$(cat ${WORKSPACE}/playground-resources/${ENVIRONMENT}/yaml/crd.yaml | grep cheImageTag:)
				CHE_VERSION=$(echo ${CHE_IMAGE} | rev | cut -d" " -f1  | rev | sed 's/"//g')
			
				BRANCH=$(echo $(echo ${CHE_VERSION} | rev | cut -d"." -f2-  | rev)".x")
				echo ${BRANCH}
	    		echo "===== PULLING THE BUILD SOURCES FROM https://github.ibm.com/ibm-api-marketplace/eclipse-che-plugin-registry ===="
            		git clone https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/eclipse-che-plugin-registry.git
			#git clone -b 7.20.x https://github.com/eclipse-che/che-plugin-registry.git
			'''
			}//closing the cred
		}//closing stage
			
		stage('Pull the extensions from playground-api-hub-extension') {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
				echo "===== cloning the api-hub-extension ====="
				sh "git clone -b master https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-api-hub-extension.git"
			}//closing the cred
		}//closing stage
		stage('Pull the extensions from playground-jupyter') {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
			echo "===== cloning the jupyter-extension ====="
			sh "git clone -b master https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-jupyter.git"
			}//closing the cred
			}//closing stage

		/*	
		stage('Pull the extensions from playground-code-pattern-explorer-extension') {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
			echo "===== cloning the api-hub-extension ====="
			sh "git clone -b master https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-code-pattern-explorer-extension.git"
			}//closing the cred
			}//closing stage
		
		stage('Pull the extensions from playground-che-theia') {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
			echo "===== cloning the playground-che-theia ====="
			sh "git clone -b master https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-che-theia.git"
			}//closing the cred
			}//closing stage
		*/
		stage('Pull the extensions from playground-che-plugin-registry') {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
				echo "===== cloning the playground-che-plugin-registry ====="
				sh "git clone -b master https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-che-plugin-registry.git"
			}//closing the cred
		}//closing stage
		
		stage('Pull the extensions from playground-helloworld') {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
				echo "===== cloning the playground-helloworld ====="
				sh "git clone -b master https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-helloworld.git"
			}//closing the cred
		}//closing stage
		/*
		stage('Pull the extensions from playground-color-theme-extension') {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
			echo "===== cloning the playground-color-theme-extension ====="
			sh "git clone -b master https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-color-theme-extension.git"
			}//closing the cred
			}//closing stage
		stage('Pull the extensions from playground-default-configuration-extension') {
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
			echo "===== cloning the playground-default-configuration-extension  ====="
			sh "git clone -b master https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-default-configuration-extension.git"
			}//closing the cred
			}//closing stage
		*/
		stage('Replacing with custom plugins') {
            
	    	sh '''#!/bin/bash
	    	
			mv ${WORKSPACE}/eclipse-che-plugin-registry ${WORKSPACE}/che-plugin-registry
			echo "========== CLEANUP THE README FILE ==========="
			rm -f ${WORKSPACE}/che-plugin-registry/README.md
			cp ${WORKSPACE}/README.md ${WORKSPACE}/che-plugin-registry/
			
				echo "=========== CLEANUP THE EXTRA PLUGIN DIRECTORIES ==========="
			shopt -s extglob
				rm -rf ${WORKSPACE}/che-plugin-registry/v3/plugins/!(che-incubator|eclipse|redhat|ms-python)/
			rm -rf ${WORKSPACE}/che-plugin-registry/v3/plugins/che-incubator/!(typescript)/
			rm -rf ${WORKSPACE}/che-plugin-registry/v3/plugins/redhat/!(vscode-didact)/
		
	    	
	    	echo "=========== CREATE THE REQUIRED DIRECTORY STRUCTURE FOR COPYING CUSTOM PLUGINS ==========="
	    	
			mkdir "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/api-hub
			mkdir "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/jupyter-server
			mkdir "${WORKSPACE}"/che-plugin-registry/v3/plugins/mongodb "${WORKSPACE}"/che-plugin-registry/v3/plugins/mongodb/mongodb-vscode "${WORKSPACE}"/che-plugin-registry/v3/plugins/mongodb/mongodb-vscode/0.2.1
			mkdir "${WORKSPACE}"/che-plugin-registry/v3/extensions "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground/api-hub 
			mkdir "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground/jupyter-server
	    	mkdir "${WORKSPACE}"/che-plugin-registry/v3/extensions/mongodb "${WORKSPACE}"/che-plugin-registry/v3/extensions/mongodb/mongodb-vscode "${WORKSPACE}"/che-plugin-registry/v3/extensions/mongodb/mongodb-vscode/0.2.1
			mkdir "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/rhm-mongodb/  "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground/rhm-mongodb
		echo "=========== COPY ALL VERSION ARTIFACTS OF API-HUB-EXTENSION ==========="
	    	cp -r "${WORKSPACE}"/playground-api-hub-extension/versions/* "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/api-hub/
	    	cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/api-hub/; mv **/*.vsix "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground/api-hub
	    	cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/api-hub; rename 's/.{7}(.*)/$1/' *
	    	VERSION1=$(cat "${WORKSPACE}"/playground-api-hub-extension/package.json | grep version | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g' | tr -d '[[:space:]]')
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/api-hub; echo $VERSION1 > latest.txt
		
		echo "=========== COPY ALL VERSION ARTIFACTS OF JUPYTER-EXTENSION ==========="
		cp -r "${WORKSPACE}"/playground-jupyter/versions/* "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/jupyter-server/
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/jupyter-server/; mv **/*.vsix "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground/jupyter-server
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/jupyter-server; rename 's/.{7}(.*)/$1/' *
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/jupyter-server; echo "0.0.1" > latest.txt
        
		#echo "=========== COPY ALL VERSION ARTIFACTS OF CODE-PATTERN-EXPLORER-EXTENSION ==========="
		#cp -r "${WORKSPACE}"/playground-code-pattern-explorer-extension/versions/* "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/code-pattern/
		#cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/code-pattern/; mv **/*.vsix "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground/code-pattern
		#cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/code-pattern; rename 's/.{7}(.*)/$1/' *
		#VERSION2=$(cat "${WORKSPACE}"/playground-code-pattern-explorer-extension/package.json | grep version | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g' | tr -d '[[:space:]]')
		#cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/code-pattern; echo $VERSION2 > latest.txt
		
		echo "=========== COPY ALL VERSION ARTIFACTS OF PLAYGROUND-CHE-THEIA ==========="
		CHE_IMAGE=$(cat ${WORKSPACE}/playground-resources/${ENVIRONMENT}/yaml/crd.yaml | grep cheImageTag:)
		CHE_VERSION=$(echo ${CHE_IMAGE} | rev | cut -d" " -f1  | rev | sed 's/"//g')
			
		cd "${WORKSPACE}"/playground-resources/versions/"${CHE_VERSION}"/playground-che-theia
		VERSION3=$(cat latest.txt) 
		
		cp "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/next/meta.yaml che-theia-meta.yaml
		
		rm -rf "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/*
		
		mkdir "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}
		cp che-theia-meta.yaml "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}/meta.yaml
		sed -i -e 's/version: next/version: '${CHE_VERSION}'/g' "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}/meta.yaml
		sed -i -e 's+quay.io/eclipse/che-theia:next+quay.io/devplayground/playground-editor:'${VERSION3}'+g' "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}/meta.yaml
		sed -i -e 's+quay.io/eclipse/che-theia-endpoint-runtime-binary:next+quay.io/devplayground/playground-endpoint-runtime-binary:'${VERSION3}'+g' "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}/meta.yaml
				
		for f in *; do
    			if [ -d "$f" ]; then
        			echo "$f"
				mkdir "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}-$f
				cp che-theia-meta.yaml "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}-$f/meta.yaml
				sed -i -e 's/version: next/version: '${CHE_VERSION}-$f'/g' "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}-$f/meta.yaml
				sed -i -e 's+quay.io/eclipse/che-theia:next+quay.io/devplayground/playground-editor:'$f'+g' "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}-$f/meta.yaml
				sed -i -e 's+quay.io/eclipse/che-theia-endpoint-runtime-binary:next+quay.io/devplayground/playground-endpoint-runtime-binary:'$f'+g' "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia/${CHE_VERSION}-$f/meta.yaml
    			fi
		done
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/eclipse/che-theia
		echo ${CHE_VERSION}-$VERSION3 > latest.txt
		cat latest.txt
		ls -l 
		
		#echo "=========== COPY the About Playground HTML/MD to v3 folder. Will be used by the theia menu ==========="
		cp "${WORKSPACE}"/playground-resources/deploy/AboutPlayground.html "${WORKSPACE}"/che-plugin-registry/v3/
		cp "${WORKSPACE}"/playground-resources/deploy/AboutPlayground.md "${WORKSPACE}"/che-plugin-registry/v3/
		
		echo "=========== COPY ALL VERSION ARTIFACTS OF Playground-HelloWorld ==========="
		cp -r "${WORKSPACE}"/playground-helloworld/versions/* "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/rhm-mongodb/
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/rhm-mongodb/; mv **/*.vsix "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground/rhm-mongodb
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/rhm-mongodb; rename 's/.{7}(.*)/$1/' *
		VERSION2=$(cat "${WORKSPACE}"/playground-helloworld/package.json | grep version | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g' | tr -d '[[:space:]]')
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/rhm-mongodb; echo $VERSION2 > latest.txt
		#echo "=========== COPY ALL VERSION ARTIFACTS OF playground-color-theme-extension  ==========="
		#cp -r "${WORKSPACE}"/playground-color-theme-extension/versions/* "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/color-theme/
		#cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/color-theme/; mv **/*.vsix "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground/color-theme
		#cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/color-theme; rename 's/.{7}(.*)/$1/' *
		#VERSION2=$(cat "${WORKSPACE}"/playground-color-theme-extension/package.json | grep version | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g' | tr -d '[[:space:]]')
		#cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/color-theme; echo $VERSION2 > latest.txt
		#echo "=========== COPY ALL VERSION ARTIFACTS OF playground-default-configuration-extension  ==========="
		#cp -r "${WORKSPACE}"/playground-default-configuration-extension/versions/* "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/default-configuration/
		#cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/default-configuration/; mv **/*.vsix "${WORKSPACE}"/che-plugin-registry/v3/extensions/ibm-playground/default-configuration
		#cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/default-configuration; rename 's/.{7}(.*)/$1/' *
		#VERSION2=$(cat "${WORKSPACE}"/playground-default-configuration-extension/package.json | grep version | head -1 | awk -F: '{ print $2 }' | sed 's/[\",]//g' | tr -d '[[:space:]]')
		#cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/ibm-playground/default-configuration; echo $VERSION2 > latest.txt
		echo "=========== COPY 3rd Party Extensions ==========="
		cp -r "${WORKSPACE}"/playground-che-plugin-registry/3rdPartyExtension/mongodb/* "${WORKSPACE}"/che-plugin-registry/v3/plugins/mongodb/mongodb-vscode/0.2.1
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/mongodb/mongodb-vscode/0.2.1; mv mongodb.mongodb-vscode-0.2.1.vsix "${WORKSPACE}"/che-plugin-registry/v3/extensions/mongodb/mongodb-vscode/0.2.1
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/mongodb/mongodb-vscode/0.2.1; 
		ls -ltr
		pwd
		cd "${WORKSPACE}"/che-plugin-registry/v3/plugins/mongodb/mongodb-vscode; echo 0.2.1 > latest.txt
		
            	'''
		}//closing stage
       
		
		stage('Build Image') {
			withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'dockerhublogin', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
				echo "===== BUILD STARTING ====="
				sh "sudo docker login --username=${MYUSER} --password=${MYTOKEN} docker.io"
				sh "cd ${WORKSPACE}/che-plugin-registry; sudo ./build.sh -r 'us.icr.io' -o 'apihub-cr' -t 'latest'"
				sh "sudo docker images"
			} //closing credentials
		}//closing stage
			
		stage('Push image to icr') {
            echo "===== Push image to ICR ====="
            withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'jenkins-ibm-cloud-cr-api-key', usernameVariable: 'CR_USER', passwordVariable: 'CR_TOKEN']]) {
      			//Push the image to ICR
      			sh "sudo docker login --username=${CR_USER} --password=${CR_TOKEN} us.icr.io"
			sh "sudo docker push us.icr.io/apihub-cr/che-plugin-registry:latest"
				//Push the same image again with a timestamp in tag
			sh "sudo docker tag us.icr.io/apihub-cr/che-plugin-registry:latest us.icr.io/apihub-cr/che-plugin-registry:${IMAGE_TAG}"
		    	sh "sudo docker push us.icr.io/apihub-cr/che-plugin-registry:${IMAGE_TAG}"
		   	
			}//Closing Credentials
		}//closing stage
		
		
		//update the latest tag string in playground-resources repository
		stage('Update the latest build information') {
		withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'apihub_gitkey', usernameVariable: 'MYUSER', passwordVariable: 'MYTOKEN']]) {
			echo "===== starting the stage ====="
			sh '''#!/bin/bash
			CHE_IMAGE=$(cat ${WORKSPACE}/playground-resources/${ENVIRONMENT}/yaml/crd.yaml | grep cheImageTag:)
			CHE_VERSION=$(echo ${CHE_IMAGE} | rev | cut -d" " -f1  | rev | sed 's/"//g')
			
			#cd ${WORKSPACE}/playground-resources/sandbox/image-tags; true > plugin-registry-latest.txt; echo '''+IMAGE_TAG+''' > plugin-registry-latest.txt
			#cd ${WORKSPACE}/playground-resources/development/image-tags; true > plugin-registry-latest.txt; echo '''+IMAGE_TAG+''' > plugin-registry-latest.txt
			
			cd ${WORKSPACE}/playground-resources/versions
			
			if [ ! -d "$CHE_VERSION" ]; then
  				mkdir $CHE_VERSION
			fi
			cd $CHE_VERSION
			if [ ! -d "playground-che-plugin-registry" ]; then
  				mkdir playground-che-plugin-registry
			fi
			
			cd playground-che-plugin-registry
			true > latest.txt;  echo '''+IMAGE_TAG+''' > latest.txt
			
			cd ${WORKSPACE}/playground-resources
			git config --global user.email 'playground@ibm.com'
			git config --global user.name 'playground'
			git add --all
			git commit -m 'updating the plugin registry build info'
			git push https://${MYUSER}:${MYTOKEN}@github.ibm.com/ibm-api-marketplace/playground-resources.git HEAD:master -f
			'''
			
		}//closing credentials
		}//closing stage
		sendmail 'success'
		cleanWs() 
		} catch (Exception e) {
				sendmail 'failure'
				cleanWs()
				sh "false"
		}
     
     }//closing node
