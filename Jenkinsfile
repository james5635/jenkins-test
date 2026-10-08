pipeline {
    agent any

    environment {
        BUN_VERSION = '1.4.2'
        PATH = "${env.HOME}/.bun/bin:${env.PATH}"
    }

    options {
        timestamps()
        disableConcurrentBuilds()
        timeout(time: 15, unit: 'MINUTES')
    }

    stages {
        stage('Setup') {
            steps {
                sh 'curl -fsSL https://bun.sh/install | bash -s "bun-v${BUN_VERSION}"'
                sh 'bun --version'
            }
        }

        stage('Install') {
            steps {
                sh 'bun install --frozen-lockfile'
            }
        }

        stage('Typecheck') {
            steps {
                sh 'bunx tsc --noEmit'
            }
        }

        stage('Test') {
            steps {
                sh '''
                    if find . -path ./node_modules -prune -o -type f \( -name "*.test.*" -o -name "*.spec.*" \) -print | grep -q .; then
                        bun test
                    else
                        echo "No test files found, skipping tests."
                    fi
                '''
            }
        }

        stage('Build') {
            steps {
                sh 'bun run build'
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'dist/**', fingerprint: true
        }
        failure {
            echo 'Build failed. Check the stage logs above.'
        }
    }
}
