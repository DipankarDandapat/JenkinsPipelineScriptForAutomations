pipeline {
    agent any
    
    parameters{
        choice(choices: ['LOCAL', 'BS'], name: 'RunMode', description: 'Select Run Mode Options : LOCAL | BS')
        string(name: 'LOC_DeviceName', defaultValue: 'ASUS_Z01RD', description: 'Enter Device Name')
        string(name: 'LOC_PlatformVersion', defaultValue: '13', description: 'Enter Android Device Version')
        string(name: 'LOC_AppiumHubURL', defaultValue: 'http://127.0.0.1:4723', description: 'Appium Hub URL.-http://127.0.0.1:4723/wd/hub')   
    }
    tools { 
        maven 'MAVEN_HOME' // Name from the Jenkins Global Configuration
        jdk 'JAVA_HOME' // Name from the Jenkins Global Configuration
    }
    
    stages { 
        stage('Checking Mandatory Fields') {
            steps {
                script{
                    if (params.RunMode == 'LOCAL') {
                        if(params.LOC_AppiumHubURL.isEmpty()) {
                            currentBuild.result = 'ABORTED'
                            error("LOC AppiumHubURL is empty")
                        }
                        if(params.LOC_DeviceName.isEmpty()) {
                            currentBuild.result = 'ABORTED'
                            error("LOC DeviceName is empty")
                        }
                        if(params.LOC_PlatformVersion.isEmpty()) {
                            currentBuild.result = 'ABORTED'
                            error("LOC Platform Version is empty")
                        }    
                    }
                }
            }
        }
        
        stage('Clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout SCM') {
            steps {
                 checkout([$class: 'GitSCM', branches: [[name: '*/master']], extensions: [], userRemoteConfigs: [[credentialsId: '2f6huot5-22c8-42f7-9ght-b566292726765', url: 'https://github.com/DipankarDandapat/JenkinsPipelineScriptForAutomations']]])
            }
        }
        
        stage('Creating Properties File') {
            steps {
                script {
                def propertiesMap = [:]

                    // Add the parameters to the properties map
                    propertiesMap['KEY1'] = params.LOC_AppiumHubURL
                    propertiesMap['KEY2'] = params.LOC_DeviceName
                    propertiesMap['KEY3'] = params.LOC_PlatformVersion
                    propertiesMap['KEY4'] = params.RunMode

                    // Specify the properties file path
                    def propertiesFilePath = './config.properties'

                    // Generate the properties content from the map
                    def propertiesContent = propertiesMap.collect { key, value ->"$key=$value"
                    }.join('\n')

                    // Write the properties content to the file
                    writeFile file: propertiesFilePath, text: propertiesContent
                }
            }
        }
		
        stage('Move Property file to Proper Destinations') {
            steps {
                script {
                    //remove existing config properties file 
                    bat 'del /src/test/resources/config.properties'
					
                    // Move the property file to the desired destination
                    bat 'move config.properties  /src/test/resources'
                }
            }
        }
        
        
        stage('Starting Execution') {
            
            steps {
                script {
              bat 'mvn -f pom.xml test '
            }
            }
        }
    post {
         always {
             publishHTML([
             allowMissing: false,
             alwaysLinkToLastBuild: true,
             keepAll: true,
             reportDir: '/extent_reports',
             reportFiles: '*.html',
             reportName: 'Extent_TestReport.html',
             reportTitles: ''])
         }
     }
}

}