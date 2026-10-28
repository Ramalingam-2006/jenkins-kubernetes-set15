
pipeline {
    agent any

    stages {
        stage('Checkout GitHub') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Ramalingam-2006/jenkins-kubernetes-set15.git'
            }
        }

        stage('Test Application') {
            steps {
                bat '''
                @echo off
                if not exist app\\index.html exit /b 1
                if not exist Dockerfile exit /b 1
                if not exist deployment.yaml exit /b 1
                findstr /C:"Set 15" app\\index.html
                if errorlevel 1 exit /b 1
                echo Application file checks passed.
                '''
            }
        }

        stage('Build Docker Image') {
            steps {
                bat '''
                @echo off
                "C:\\Users\\akkin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe" build -t ramalingam2006/jenkins-kubernetes-set15:latest .
                if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds-set15',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_TOKEN'
                )]) {
                    bat '''
                    @echo off
                    setlocal DisableDelayedExpansion
                    echo %DOCKER_TOKEN%| "C:\\Users\\akkin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe" login -u "%DOCKER_USER%" --password-stdin
                    if errorlevel 1 exit /b 1
                    "C:\\Users\\akkin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\docker.exe" push ramalingam2006/jenkins-kubernetes-set15:latest
                    if errorlevel 1 exit /b 1
                    '''
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                bat '''
                @echo off
                "C:\\Users\\akkin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\kubectl.exe" --kubeconfig="C:\\Users\\akkin\\Downloads\\jenkins\\kubeconfig.yaml" apply -f deployment.yaml
                if errorlevel 1 exit /b 1
                '''
            }
        }

        stage('Verify Deployment') {
            steps {
                bat '''
                @echo off
                "C:\\Users\\akkin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\kubectl.exe" --kubeconfig="C:\\Users\\akkin\\Downloads\\jenkins\\kubeconfig.yaml" rollout status deployment/final-web --timeout=180s
                if errorlevel 1 exit /b 1
                "C:\\Users\\akkin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\kubectl.exe" --kubeconfig="C:\\Users\\akkin\\Downloads\\jenkins\\kubeconfig.yaml" get deployment final-web
                "C:\\Users\\akkin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\kubectl.exe" --kubeconfig="C:\\Users\\akkin\\Downloads\\jenkins\\kubeconfig.yaml" get pods
                "C:\\Users\\akkin\\AppData\\Local\\Programs\\DockerDesktop\\resources\\bin\\kubectl.exe" --kubeconfig="C:\\Users\\akkin\\Downloads\\jenkins\\kubeconfig.yaml" get service final-web-service
                '''
            }
        }
    }

    post {
        success {
            echo 'Set 15 completed: build, test, push and Kubernetes deployment succeeded.'
        }
        failure {
            echo 'Set 15 failed. Inspect Console Output and identify the first failed stage.'
        }
    }
}
