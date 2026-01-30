pipeline {
 agent any
 environment {
 // JMeter installation directory
 JMETER_HOME = "D:\JMeter_data\apache-jmeter-5.6.3"
 PATH = "${JMETER_HOME}\\bin;${env.PATH}"
 }
 stages {
 stage('Checkout') {
 steps {
 git branch: 'main',
 url: 'https://github.com/Sumitjicy/Test_JMeter.git'
 }
 }
 stage('Build') {
 steps {
 echo "Building the project..."
 }
 }
 stage('Test') {
 steps {
 echo "Running JMeter tests..."
 // Clean previous artifacts
 bat '''
 if exist results.jtl del /f /q results.jtl
 if exist jmeter-report rmdir /s /q jmeter-report
 '''
 // Run JMeter non-GUI
 bat "\"%JMETER_HOME%\\bin\\jmeter.bat\" -n -t \"%WORKSPACE%\\Jmeter_File\\Spike_Test.jmx\" -l
results.jtl -e -o jmeter-report"
 // Optional: list report folder
 bat 'dir jmeter-report /s'
 }
 }
 stage('Deploy') {
 steps {
 echo "Deploying application..."
 }
 }
 }
 post {
 success {
 echo "Publishing JMeter reports in Jenkins"
 script {
 // 1️⃣Publish HTML report
 if (fileExists('jmeter-report/index.html')) {
 publishHTML(target: [
 allowMissing: false,
 alwaysLinkToLastBuild: true,
 keepAll: true,
 reportDir: 'jmeter-report',
 reportFiles: 'index.html',
 reportName: 'JMeter Test HTML Report',
 useWrapperFileDirectly: true
 ])
 }
 // 2️⃣Publish Performance Plugin graphs
 if (fileExists('results.jtl')) {
 perfReport(
 sourceDataFiles: 'results.jtl',
 errorFailedThreshold: 1,
 errorUnstableThreshold: 0
 )
 }
 
 }
 }
 failure {
 echo "Pipeline failed!"
 
 }
 }
}