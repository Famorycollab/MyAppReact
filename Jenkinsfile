pipeline {
    agent any

    environment {
        COMPOSE_PROJECT_NAME = 'myappreact'
    }
    //récuperation du code source

    stages {
        stage('Checkout') {
            steps {
                echo '=== Étape 1 : Récupération du code source ==='
                checkout scm
            }
        }
     // costruction des images docker
        stage('Build Docker Images') {
            steps {
                echo '=== Étape 2 : Construction des images Docker ==='
                script {
                    if (isUnix()) {
                        sh 'docker compose build'
                    } else {
                        bat 'docker compose build'
                    }
                }
            }
        }
     // lancement des conteneurs 
        stage('Deploy / Run Application') {
            steps {
                echo '=== Étape 3 : Démarrage des conteneurs en arrière-plan ==='
                script {
                    if (isUnix()) {
                        sh 'docker compose up -d'
                    } else {
                        bat 'docker compose up -d'
                    }
                }
            }
        }
      // verification de l'etat des services   

        stage('Health Check') {
            steps {
                echo '=== Étape 4 : Vérification de l\'état des services ==='
                script {
                    if (isUnix()) {
                        sh 'docker compose ps'
                    } else {
                        bat 'docker compose ps'
                    }
                }
            }
        }
    }
// affichage des logs en cas d'echec 
    post {
        always {
            echo 'Pipeline terminé.'
        }
        failure {
            echo 'Échec du déploiement !'
            script {
                if (isUnix()) {
                    sh 'docker compose logs --tail=50'
                } else {
                    bat 'docker compose logs --tail=50'
                }
            }
        }
    }
}
