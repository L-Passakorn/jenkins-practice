pipeline {
    agent any
    parameters {
        booleanParam(name: 'RUN_DEPLOY', defaultValue: false, description: 'Should we deploy?')
        choice(name: 'ENVIRONMENT', choices: ['dev', 'staging', 'prod'], description: 'Select deployment environment')
    }
    stages {
        stage('Build') {
            steps {
                echo 'Building application...'
            }
        }
        
        stage('Unit Tests') {
            when {
                expression { currentBuild.currentResult == 'SUCCESS' }
            }
            steps {
                echo 'Running unit tests...'
                sh 'sleep 5'
            }
        }
        
        stage('Test in Parallel') {
            parallel {
                stage('Linux Testing') {
                    steps {
                        echo 'Simulating testing on Linux...'
                        sh 'echo "Testing on Linux OS - All tests passed!"'
                    }
                }
                stage('Windows Testing') {
                    steps {
                        echo 'Simulating testing on Windows...'
                        sh 'echo "Testing on Windows OS - All tests passed!"'
                    }
                }
            }
        }
        
        stage('Integration Tests') {
            steps {
                echo 'Running integration tests...'
                sh 'sleep 5'
            }
        }
        
        stage('Test Results') {
            steps {
                sh 'echo "All tests passed!" > results.txt'
                archiveArtifacts artifacts: 'results.txt', fingerprint: true
            }
        }
        
        stage('Approval') {
            steps {
                timeout(time: 2, unit: 'MINUTES') {
                    input message: "Do you want to proceed with deployment to ${params.ENVIRONMENT}?", 
                          ok: 'Deploy',
                          submitterParameter: 'APPROVER'
                }
            }
        }

        stage('Deploy') {
            when {
                allOf {
                    expression { return params.RUN_DEPLOY }
                    expression { currentBuild.currentResult == 'SUCCESS' }
                }
            }
            steps {
                echo "Deploying application to ${params.ENVIRONMENT} environment..."
                echo "Environment selected: ${params.ENVIRONMENT}"
                echo "Approved by: ${env.APPROVER}"
            }
        }
    }
    post {
        success {
            echo '✅ Pipeline finished successfully!'
        }
        failure {
            echo '❌ Pipeline failed. Check logs!'
        }
        aborted {
            echo '⏰ Pipeline was aborted (likely due to timeout or manual abort).'
        }
        always {
            echo 'Pipeline completed (success, failure, or aborted).'
        }
    }
}