pipeline {
    agent any

    stages {
        stage('Presentation') {
            steps {
                echo "Bonjour, je m'appelle Nidhal, je suis en 3ème année RSI."
            }
        }
    }

    post {
        success {
            echo "Pipeline terminé avec succès."
        }
        failure {
            echo "Pipeline échoué."
        }
    }
}

