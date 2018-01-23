node {
    stage('Create Environment') {
        // extract the branch and project name and create an OpenShift project with the same name
        script {
            sh '''
                folder=`echo $JOB_NAME | cut -d/ -f1 | awk '{print tolower($0)}'`
                branch=`echo $BRANCH_NAME | awk '{print tolower($0)}'`
                set +e
                oc get projects | grep "$folder-$branch"
                if [ $? -ne 0 ]; then
                    oc new-project "$folder-$branch" &&
                    #we assume people are in the group which corresponds to the folder name
                    oc adm policy add-role-to-group admin $folder --namespace="$folder-$branch" &&
                    wget https://raw.githubusercontent.com/Liatrio-LOK/microservices-demo/master/deploy/openshift/templates/full_stack_namespace_template.yaml &&
                    oc create -f full_stack_namespace_template.yaml &&
                    rm full_stack_namespace_template.yaml
                fi 
            '''
        }
    }

    stage('Build Image') {
        // build configurations only build the master branch unless otherwise specified
        // pull out the latest commit hash to identify what we want to build
        script {
            commitId = sh (
              script: ''' 
                      git ls-remote git://github.com/Liatrio-LOK/front-end.git \
                      | grep ${BRANCH_NAME} | cut -f 1
                      ''',
              returnStdout: true
            ).trim()
            
            print commitId
        } 
    }

    stage('Build Image') {
        openshiftBuild (
            namespace: 'sock-shop', 
            bldCfg: 'shipping-build', 
            checkForTriggeredDeployments: 'true', 
            showBuildLogs: 'true', 
            verbose: 'false'
        )
    }

    stage('Tag Image') {
        openshiftTag (
            namespace:  'sock-shop',
            sourceStream: 'shipping',
            sourceTag: 'latest',
            destinationStream: 'shipping',
            destinationTag: env.BRANCH_NAME,
            destinationNamespace: 'sock-shop'
        )
    }
}
