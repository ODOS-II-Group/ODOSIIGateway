//get common library inclusion
def Common = new odos.jenkins.Common()

pipeline {
    agent any

    stages {
        stage('init'){
          steps{
            script{
              Common.runGitMerge('Staging', 'master')
              Common.slack "Build Started."
            }
          }
        }
        stage('Build') {
            steps {
                script{
                  Common.slack 'Building...'
                  sh """
                    yarn
                    yarn install
                  """
                  Common.jHipsterBuild()
                }
            }
        }
        stage('Sonar Scan') {
          steps {
            script{
              Common.slack 'Sonar Scan and Upload...'
              Common.sonarScan()
            }
          }
        }
        stage('Fortify Scan') {
            steps {
              script{
                Common.slack 'Fortify Scan...'
                Common.fortify('src','reports', 4)
              }
            }
        }
        stage('Build Container') {
            steps {
              script{
                Common.slack 'Packaging into a container...'
                Common.buildContainer('odosiigateway')
              }
            }
        }
        stage('Twistlock Scan') {
            steps {
              script{
                Common.slack 'Twistlock Scan...'
                Common.twistlock('docker.lassiterdynamics.com:5000', 'odosiigateway','latest')
              }
            }
        }
        stage('Push Container') {
            steps {
              script{
                Common.slack 'Push to Docker Registry..'
                Common.pushContainer('odosiigateway')
              }
            }
        }
        stage('Test Deploy') {
            steps {
              script{
                Common.slack 'Deploying to Test Environment...'
                Common.deployToOpenShift('odos-test','odosiigateway','latest')
              }
            }
        }
        stage('FT') {
            steps {
              script{
                Common.slack 'Functional Testing...'
                Common.runFT('odosiigateway')
              }
            }
        }
        stage('PT') {
            steps {
              script{
                Common.slack 'Performance Testing...'
                Common.runPT('odosiigateway')
              }
            }
        }
        stage('Merge') {
            steps {
              script{
                Common.slack 'Merge to master branch...'
                Common.runGitPush('master')
              }
            }
        }
        stage('PP Deploy') {
            steps {
              script{
                Common.slack 'Deploying to PreProd Environment...'
              }
            }
        }

    }
}
