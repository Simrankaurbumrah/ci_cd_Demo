pipeline {
    agent any

    // -----------------------------------------
    //  Global Constants (immutable-ish values)
    environment {
        ARTIFACT_DIR = 'artifacts'
        REPORT_DIR   = 'reports'
        CACHE_DIR    = '.ci-cache'
    }

    // -----------------------------------------
    //  Parameters (for flexibility)
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: 'Git branch to build')
        booleanParam(name: 'RUN_TESTS', defaultValue: true, description: 'Run tests?')
    }

    options {
        timestamps()      // Add timestamps
        ansiColor('xterm') // Colored logs
        buildDiscarder(logRotator(numToKeepStr: '10')) // keep last 10 builds
        timeout(time: 30, unit: 'MINUTES') // Fail after 30 min
    }

    tools {
        jdk "JDK21"
        maven "Maven3"
        nodejs "NodeJS"
    }

    stages {

        // ------------------------------
        stage('Checkout') {
            steps {
                echo "Checking out code from branch: ${params.BRANCH_NAME}"
                git branch: params.BRANCH_NAME,
                  url: 'https://github.com/Simrankaurbumrah/ci_cd_Demo.git'
                  // credentialsId: 'your-github-creds'

                // checkout scm
            }
        }

        // ------------------------------
        stage('Parallel Build & Test') {
            parallel {
                stage('Java Build') {
                    steps {
                        echo "Running Maven build..."
                    }
                }

                stage('Node Build') {
                    steps {
                        echo "Installing npm packages..."
                    }
                }

                stage('Python Scripts') {
                    steps {
                        echo "Executing modular Python script..."
                        script {
                            def modules = ["etl.py", "validation.py", "reporting.py"]
                            for (mod in modules) {
                                echo "Running ${mod}"
                                sh "python3 scripts/${mod}"
                            }
                        }
                    }
                }

                stage('Quality Gates') {
                    when { expression { return params.RUN_TESTS } }
                    steps {
                        echo "Running tests & quality checks..."
                    }
                }
            }
        }

        // ------------------------------
        stage('Artifacts & Caching') {
            steps {
                echo "Archiving build artifacts to ${ARTIFACT_DIR}"
                echo "Using cache directory: ${CACHE_DIR}"
                // archiveArtifacts artifacts: 'target/*.jar'
                // stash/unstash for cache
            }
        }

        // ------------------------------
        stage('Security & Credentials') {
            steps {
                withCredentials([string(credentialsId: 'my-api-token', variable: 'API_TOKEN')]) {
                    echo "Using secret API token safely (not printed)"
                }
            }
        }

        // ------------------------------
        stage('Deployment') {
            // agent { label 'prod-nodes' } // Scalability: distribute build
            steps {
                echo "Deploying application to production..."
            }
        }
    }

    // -----------------------------------------
    post {
        success {
            echo " Build succeeded!"
            // Slack / Email notification
            // slackSend channel: '#ci-cd', message: "Build Success"
        }
        failure {
            echo " Build failed!"
        }
        always {
            echo "Cleaning up workspace..."
            cleanWs() // Workspace cleanup
        }
    }

}


