node {
    def server
    def rtGradle = Artifactory.newGradleBuild()
    def buildInfo = Artifactory.newBuildInfo()

    stage ('Git Clone') {
        git url: 'https://github.com/sr-ota/gradle-jfrog-example', branch: 'main'
    }
    
    stage ('Artifactory configuration') {
        // Obtain an Artifactory server instance, defined in Jenkins --> Manage Jenkins --> Configure System:
        server = Artifactory.server "jfrogeval"
        rtGradle.tool = "latest"
        rtGradle.deployer repo: "local-snapshots", server: server
        rtGradle.resolver repo: "gradle-virtual", server: server
    }

    stage ('Config Build Info') {
        buildInfo.env.capture = true
        buildInfo.env.filter.addInclude ("*")
        // buildInfo.env.filter.addExclude ("DONT_COLLECT*")
    }

    stage ('Extra gradle configurations') {
        // rtGradle.deployer.artifactDeploymentPatterns.addExclude("*.war")
        rtGradle.usesPlugin = true // Artifactory plugin already defined in build script
        // rtGradle.useWrapper = true
    }
    
    stage ('Exec Gradle') {
        sh 'ls -alh'
        sh 'echo $PWD'
        sh "find . name '*.jar'"
        rtGradle.run rootDir: ".", tasks: 'clean build artifactoryPublish', buildInfo: buildInfo
    }

    stage ('Publish build info') {
        server.publishBuildInfo buildInfo
    }
}
