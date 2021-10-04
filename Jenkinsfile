node {
    //URL to Github repository https://github.com/<owner>/<repo>
    def GITHUB_REPO_URL="https://github.com/oznbgdemo/demo-fe"
    //CredentialId for the GitHub repository
    def CREDENTIALS_ID = "osmanburucu-ibm"
    //Retrieve ENV ID values for DEV and QA from "Download attributes from API" json file
    def VELOCITY_ENV_ID_INPUT="64fa7beb-4deb-4177-84a0-c018a32bff8e"
    def VELOCITY_ENV_ID_DEV="def86393-cbbf-40b2-a97a-fb08c7cf64b5"
    def VELOCITY_ENV_ID_QA="97002b7a-9de3-4c05-a233-a13669e16438"
    def VELOCITY_ENV_ID_PROD="e4a162e6-badb-4cf3-8600-268e388b653e"
	
    //VELOCITY_APP_NAME must match your Velocity pipeline application name
    def VELOCITY_APP_NAME="FE"
    def VELOCITY_APP_ID="b151593e-73a1-4fb4-8d7a-11b0023134c9"
    //Version number 
    def VERSION_NUMBER="${MAJOR_VERSION}.${MINOR_VERSION}"
    //Do not change below this line.
    def GIT_COMMIT
    
    stage ('cloning the repository'){
        currentBuild.displayName = "${VERSION_NUMBER}.${BUILD_NUMBER}"
        echo currentBuild.displayName = "${currentBuild.displayName}"
        majorVersion="${BUILD_NUMBER}"
        def scm = git branch: 'main', url: "${GITHUB_REPO_URL}",credentialsId: "${CREDENTIALS_ID}"
        GIT_COMMIT = sh(returnStdout: true, script: "git rev-parse HEAD").trim()
        echo "GIT_COMMIT=${GIT_COMMIT}"
    }
    
    stage ('build') {
        sh '''echo $WORKSPACE
	      echo 'building in stage build'
	      zip ${WORKSPACE}/data.zip *.*
              '''
    }
        
    stage ('Send metrics to UCV') {
        echo "Building ${VELOCITY_APP_NAME} (Build:${currentBuild.displayName}, GIT_COMMIT:${GIT_COMMIT})"
        step($class: 'UploadBuild',
            tenantId: "5ade13625558f2c6688d15ce",
            revision: "${GIT_COMMIT}",
            appName: "${VELOCITY_APP_NAME}",
            versionName:"${currentBuild.displayName}",
            requestor: "admin", id: "${currentBuild.displayName}")
	step([$class: 'UCDeployPublisher', component: [componentName: 'GIT-FE', componentTag: '', delivery: [$class: 'Push', baseDir: "${WORKSPACE}", fileExcludePatterns: '', fileIncludePatterns: "data.zip", pushDescription: '', pushFailedBuild: false, pushIncremental: false, pushProperties: '', pushVersion: "${currentBuild.displayName}"]], siteName: 'UCD'])
    }

}
