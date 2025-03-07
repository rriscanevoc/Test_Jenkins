pipeline {
    agent any
    
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        
        stage('Detect PR Merge') {
            steps {
                script {
                    // Obtener el mensaje del último commit
                    def lastCommitMessage = sh(
                        script: "git log -1 --pretty=%B",
                        returnStdout: true
                    ).trim()
                    
                    echo "Último mensaje del commit: ${lastCommitMessage}"
                    
                    // Verificar si es un merge de un PR
                    if (lastCommitMessage =~ /Merge pull request #\d+/) {
                        echo "PR Mergeado detectado, continuando con el pipeline..."
                    } else {
                        echo "No es un merge de PR, deteniendo pipeline."
                        error("Pipeline detenido porque no es un merge de PR.")
                    }
                }
            }
        }
        
        stage('Show Changed Files') {
            steps {
                script {
                    def changedFiles = sh(
                        script: "git diff --name-status HEAD~1 HEAD",
                        returnStdout: true
                    ).trim()
                    
                    echo "Archivos modificados en el merge:"
                    echo "${changedFiles}"
                    
                    // Guardar lista de archivos para uso posterior
                    writeFile file: 'changed-files.txt', text: changedFiles
                    archiveArtifacts artifacts: 'changed-files.txt', fingerprint: true
                }
            }
        }
    }
    
    post {
        always {
            cleanWs()
        }
    }
}
