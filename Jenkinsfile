pipeline {
    environment {
        git_project = 'https://github.com/chrishowejones/my-scala-project.git'
        commit_hash = 'none'
        CHECKOUT = 'master'
        CREDENTIALS_ID ='makcorp-312512'
        BUCKET = 'staging.makcorp-312512.appspot.com'
        PATTERN = '/var/lib/jenkins/workspace/test-scala/target/scala-2.12/my-scala-project_2.12-0.1.0.jar'
    }
    agent any

    stages {
        stage('Сlone the repository') {
            steps {
                script {
                    def scmVars = checkout([$class: 'GitSCM',
                    branches: [[name: "${CHECKOUT}"]],
                    doGenerateSubmoduleConfigurations: false,
                    extensions: [[$class: 'CleanBeforeCheckout']],
                    submoduleCfg: [], 
                    userRemoteConfigs: [[
                    url: git_project]]])
                }
            }
        }
        stage('Build') {
            steps {
                echo "Compiling..."
                sh "${tool name: 'test', type: 'org.jvnet.hudson.plugins.SbtPluginBuilder$SbtInstallation'}/bin/sbt compile"
            }
        }
        stage('Build package') {
            steps {
                echo "Compiling run..."
                sh "${tool name: 'test', type: 'org.jvnet.hudson.plugins.SbtPluginBuilder$SbtInstallation'}/bin/sbt package"
            }
        }
        stage('Build run') {
            steps {
                echo "Compiling run..."
                sh "${tool name: 'test', type: 'org.jvnet.hudson.plugins.SbtPluginBuilder$SbtInstallation'}/bin/sbt run"
            }
        }
        stage('Store to GCS') {
            steps{
                sh '''
                    env > build_environment.txt
                '''
                // If we name pattern build_environment.txt, this will upload the local file to our GCS bucket.
                step([$class: 'ClassicUploadStep', credentialsId: env
                        .CREDENTIALS_ID,  bucket: "gs://${env.BUCKET}",
                      pattern: env.PATTERN])
            }
        }
    }
}
