pipeline {
    agent any

    environment {
        checkoutDir = "${env.WORKSPACE}"
    }

    stages {
        stage('Initialize Core and Clean Old Files') {
            steps {
                script {
                    echo "========= Initializing Core and Cleaning the old files ========="
                    
                    // Cleaning the framework_core
                    dir("${checkoutDir}/framework_core") {
                        sh '''
                            git reset --hard
                            git clean -fdx
                            framework_core_branch_name=$(git rev-parse --abbrev-ref HEAD)
                            echo "framework_core branch -> $framework_core_branch_name"
                        '''
                    }
                    
                    // Cleaning the games_development and checking for lfs to be installed properly
                    dir("${checkoutDir}/games_development") {
                        sh '''
                            git lfs uninstall
                            git reset --hard
                            git lfs install
                            git clean -fdx
                            games_development_branch_name=$(git rev-parse --abbrev-ref HEAD)
                            echo "games_development branch -> $games_development_branch_name"
                        '''
                    }
                    
                    // Cleaning the game source file
                    dir("${checkoutDir}/games_sources") {
                        sh '''
                            git reset --hard
                            git clean -fdx
                            games_sources_branch_name=$(git rev-parse --abbrev-ref HEAD)
                            echo "games_sources branch -> $games_sources_branch_name"
                        '''
                    }
                }
            }
        }

        stage('Check Versions') {
            steps {
                script {
                    echo "========= NVM, Node, Yarn and NPM Versions ========="
                    
                    sh '''
                        source ~/.bashrc
                        nvm_version=$(nvm -v)
                        node_version=$(node -v)
                        npm_version=$(npm -v)
                        yarn_version=$(yarn -v)
                        echo "nvm version -> $nvm_version"
                        echo "node version -> $node_version"
                        echo "npm version -> $npm_version"
                        echo "yarn version -> $yarn_version"
                    '''
                }
            }
        }

        stage('Install Framework and Sources') {
            steps {
                script {
                    echo "========= Installing Framework and Sources ========="
                    
                    dir("${checkoutDir}/framework_core") {
                        sh 'yarn install'
                    }

                    dir("${checkoutDir}/games_sources") {
                        sh 'yarn install'
                    }
                }
            }
        }

        stage('Install Tools') {
            steps {
                script {
                    echo "========= Installing Tools ========="
                    
                    dir("${checkoutDir}/development_tools/build_games_new") {
                        sh 'npm install'
                    }
                }
            }
        }

        stage('Run Tools') {
            steps {
                script {
                    echo "========= Running Tools ========="
                    
                    dir("${checkoutDir}/development_tools/build_games_new") {
                        sh 'node -e \'require("./teamcity_compiler").getTeamcityDataConfiguration()\''
                    }
                }
            }
        }
    }
}
