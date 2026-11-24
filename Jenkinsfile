pipeline {
    agent any

    environment {
        COMPOSE_PROJECT_NAME = 'myappreact'
    }

    stages {
        stage('Checkout') {
            steps {
                echo '=== Étape 1 : Récupération du code source ==='
                checkout scm
            }
        }

        stage('Build Docker Images') {
            steps {
                echo '=== Étape 2 : Construction des images Docker ==='
                script {
                    def composeCmd = getComposeCommand()
                    runCmd("${composeCmd} build")
                }
            }
        }

        stage('Deploy / Run Application') {
            steps {
                echo '=== Étape 3 : Démarrage des conteneurs en arrière-plan ==='
                script {
                    def composeCmd = getComposeCommand()
                    runCmd("${composeCmd} up -d")
                }
            }
        }

        stage('Health Check') {
            steps {
                echo '=== Étape 4 : Vérification de l\'état des services ==='
                script {
                    def composeCmd = getComposeCommand()
                    runCmd("${composeCmd} ps")
                }
            }
        }
    }

    post {
        always {
            echo 'Pipeline terminé.'
        }
        failure {
            echo 'Échec du déploiement !'
        }
    }
}

def runCmd(cmd) {
    if (isUnix()) {
        sh cmd
    } else {
        bat cmd
    }
}

def getComposeCommand() {
    if (isUnix()) {
        def hasPlugin = sh(script: 'docker compose version', returnStatus: true) == 0
        return hasPlugin ? 'docker compose' : 'docker-compose'
    } else {
        def hasPlugin = bat(script: 'docker compose version', returnStatus: true) == 0
        if (hasPlugin) {
            return 'docker compose'
        }
        def hasCompose = bat(script: 'docker-compose version', returnStatus: true) == 0
        if (hasCompose) {
            return 'docker-compose'
        }
        // Chemin direct Docker Desktop si non présent dans le PATH du service Jenkins
        return '"C:\\Users\\Famory Toure\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker-compose.exe"'
    }
}
