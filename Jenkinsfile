def getBranchName() {
  return scm.branches[0].name
}

node{
stage('Checkout') {
  echo 'getting branch ' + getBranchName() + '...'
checkout([
$class: 'GitSCM',
branches: [[name: getBranchName()]],
doGenerateSubmoduleConfigurations: false,
extensions: [], submoduleCfg: [],
userRemoteConfigs: [[
name: 'github',
url: 'https://github.com/Deepthikulkarni/iOSSample.git'
]]
])
}

stage('Build') {
sh "agvtool next-version -all" 
sh "xcrun xcodebuild  -project Deepthi.xcodeproj -scheme Deepthi -destination 'name=iPhone 7' clean build"
}

stage('Archieve') {
sh "xcodebuild -scheme Deepthi -sdk iphoneos -target Deepthi -destination 'generic/platform=iOS' -archivePath export/Deepthi.xcarchive archive DEVELOPMENT_TEAM='LSVVD8ZPH6' PROVISIONING_PROFILE='54763f3e-f511-4a5c-8c5d-297fb34c2bd8' CODE_SIGNING_REQUIRED=NO"
}

stage('Extracting ipa') {
sh "security unlock-keychain -p admin"
sh "xcodebuild -exportArchive -archivePath export/Deepthi.xcarchive \
-exportPath exportedIPA/ \
-exportOptionsPlist ~/.jenkins/workspace/CodeSigning/ExportOptions.plist"
}

stage ('Uploading to Hockey'){
hockeyApp applications: [[apiToken: 'cc4be45686e24610a5d735e0cea56157', downloadAllowed: true, filePath: '**/Deepthi-*.ipa', mandatory: false, notifyTeam: true, releaseNotesMethod: none(), uploadMethod: appCreation(false)]], debugMode: false, failGracefully: false
}
}
