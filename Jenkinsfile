pipeline {

    agent {
        dockerfile {
          filename 'Dockerfile'
          args '-u="root"'
          reuseNode true
        }
    }
    triggers {
        cron '@daily'
    }
    stages {
        stage('Checkout') {
            steps {
                // Get code
                checkout scm
                // Insert .gh.json for gh CLI
                withCredentials([file(credentialsId: 'gh-config-json', variable: 'CONFIG')]) {
                  sh 'cat ${CONFIG} > /root/.gh.json'
                }
            }
        }

        stage('Handle pull request') {
            parallel {
                stage('Verify pull request'){
                    steps {
                        script {
                            try {
                                echo "Verify that that there is a file with user s name in it, ignoring case"
                                sh 'find . -iname $CHANGE_AUTHOR | grep -i $CHANGE_AUTHOR'
                                sh 'echo all tests successful, add a comment in the PR'
                                sh 'gh pr ${CHANGE_ID} --comment "Congratulations :+1:! Your pull request went through automated checks and looks all good. I will be merging it within 24h.\n\nMore details on [Jenkins](${RUN_DISPLAY_URL})."'
                            }
                            catch (error) {
                                sh 'gh pr ${CHANGE_ID} --comment "Almost! But there was an error checking your commit. Please make sure that you use your short name for the file name, **and** that it contains your short name: **${CHANGE_AUTHOR}**.\n\nMore details on [Jenkins](${RUN_DISPLAY_URL})."'
                                throw error
                            }
                        }
                    }
                }
                stage('Merge verified pull requests'){
                    steps {
                        withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'pdxc-jenkins', usernameVariable: 'GIT_USERNAME', passwordVariable: 'GIT_PASSWORD']]) {
                            sh './scripts/merge_pull_requests.sh'
                        }
                    }
                }
            }
        }
    }
}
