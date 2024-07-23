pipeline {
    agent any
    
    environment {
        // Define environment variables
        DOCKER_IMAGE = "ssepulvedacl/my-spring-boot-app"
        REGISTRY = "hub.docker.com" // e.g., Docker Hub or any other registry
        SONARQUBE_URL = 'http://localhost:9000' // Cambia esto a la URL de tu servidor SonarQube
        SONARQUBE_TOKEN = 'squ_fbd2a41f9e439a4ff3993ef20df8a53e3214c8dc' // Cambia esto a tu token de SonarQube
    }

    stages {
        stage('Checkout') {
            steps {
                // Clona el repositorio
                git branch: 'main', url: 'https://github.com/CheloK4nt/my-spring-boot-app.git'
            }
        }
        stage('Build') {
            steps {
                // Construye el proyecto Maven y ejecuta el análisis de SonarQube
                withSonarQubeEnv('SonarQube') {
                    bat 'mvn clean package sonar:sonar'
                }
            }
        }
        stage('SonarQube Quality Gate') {
            steps {
                // Esperar el resultado del análisis de SonarQube
                script {
                    def qg = waitForQualityGate()
                    if (qg.status != 'OK') {
                        error "Pipeline aborted due to quality gate failure: ${qg.status}"
                    }
                }
            }
        }
        // stage('Build Docker Image') {
        //     steps {
        //         script {
        //             // Construye la imagen Docker
        //             def image = docker.build("${env.DOCKER_IMAGE}:${env.BUILD_ID}")
        //             // Inicia sesión en el registro Docker
        //             docker.withRegistry("https://${env.REGISTRY}", 'docker-credentials-id') {
        //                 // Empuja la imagen al registro
        //                 image.push()
        //             }
        //         }
        //     }
        // }
        // stage('Deploy to Kubernetes') {
        //     steps {
        //         script {
        //             // Crea un archivo de despliegue para Kubernetes
        //             bat """
        //             cat <<EOF > k8s-deployment.yaml
        //             apiVersion: apps/v1
        //             kind: Deployment
        //             metadata:
        //               name: my-spring-boot-app
        //               labels:
        //                 app: my-spring-boot-app
        //             spec:
        //               replicas: 1
        //               selector:
        //                 matchLabels:
        //                   app: my-spring-boot-app
        //               template:
        //                 metadata:
        //                   labels:
        //                     app: my-spring-boot-app
        //                 spec:
        //                   containers:
        //                   - name: ssepulvedacl/my-spring-boot-app
        //                     image: ${env.REGISTRY}/${DOCKER_IMAGE}:${env.BUILD_ID}
        //                     ports:
        //                     - containerPort: 8080
        //             ---
        //             apiVersion: v1
        //             kind: Service
        //             metadata:
        //               name: my-spring-boot-app-service
        //             spec:
        //               type: LoadBalancer
        //               ports:
        //               - port: 80
        //                 targetPort: 8080
        //               selector:
        //                 app: my-spring-boot-app
        //             EOF
        //             """

        //             // Despliega en Kubernetes
        //             //withKubeConfig([credentialsId: env.KUBE_CONFIG_PATH]) {
        //                 bat 'kubectl apply -f k8s-deployment.yaml'
        //             //}
        //         }
        //     }
        // }
        stage('Cleanup') {
            steps {
                // Limpieza después de cada build
                cleanWs()
            }
        }
    }
}
