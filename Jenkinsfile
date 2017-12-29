node {
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
