pipeline {
    agent any
    tools {
        maven "maven"
    }
    stages {
         stage('Build') {
            steps {
               bat "mvn -B -U -e -V clean -DskipTests package"
            }
        }
        stage('Test') {
            steps {
              echo "skipping test"
       //        bat "mvn test -Dhttp.port=8093"
            }
        }
        stage('Publish to Nexus Repository') {
			
            steps {
                script {
                    pom = readMavenPom file: "pom.xml";
                    filesByGlob = findFiles(glob: "target/*.jar");
                   // echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    artifactPath = filesByGlob[0].path;
                    artifactExists = fileExists artifactPath;
			propertyFileByGlob = findFiles(glob: "*.properties");
			propertiesFile =  propertyFileByGlob[0].path;
                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";
                        nexusArtifactUploader (
                            nexusVersion: NEXUS_VERSION,
                            protocol: NEXUS_PROTOCOL,
                            nexusUrl: NEXUS_URL,
                            groupId: pom.groupId,
			    version: "${BUILD_NUMBER}",
                            repository: NEXUS_REPOSITORY,
                            credentialsId: "NEXUS_CREDENTIALS",
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: artifactPath,
                                type: "jar"],
                                // Lets upload the pom.xml file for additional information for Transitive dependencies
                                [artifactId: pom.artifactId,
                                classifier: '',
                                file: propertiesFile,
                                type: "properties"]
                            ]
                        );
                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
		}	
	}
	
}
