pipeline {
    agent any

    stages {

        stage('Build JAR') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t fullstack-backend .'
            }
        }

        stage('Stop Old Container') {
            steps {
                sh '''
                    docker stop fullstack-container || true
                    docker rm fullstack-container || true
                '''
            }
        }

        stage('Create Env File') {
            steps {
                withCredentials([
                    string(credentialsId: 'DB_URL', variable: 'DB_URL'),
                    string(credentialsId: 'DB_USERNAME', variable: 'DB_USERNAME'),
                    string(credentialsId: 'DB_PASSWORD', variable: 'DB_PASSWORD'),
                    string(credentialsId: 'JWT_SECRET', variable: 'JWT_SECRET'),
                    string(credentialsId: 'MAIL_USERNAME', variable: 'MAIL_USERNAME'),
                    string(credentialsId: 'MAIL_PASSWORD', variable: 'MAIL_PASSWORD'),
                    string(credentialsId: 'AWS_ACCESS_KEY', variable: 'AWS_ACCESS_KEY'),
                    string(credentialsId: 'AWS_SECRET_KEY', variable: 'AWS_SECRET_KEY'),
                    string(credentialsId: 'AWS_REGION', variable: 'AWS_REGION'),
                    string(credentialsId: 'AWS_BUCKET_NAME', variable: 'AWS_BUCKET_NAME'),
                    string(credentialsId: 'SERVER_PORT', variable: 'SERVER_PORT')
                ]) {
                    sh '''
                        cat <<EOF > app.env
DB_URL=$DB_URL
DB_USERNAME=$DB_USERNAME
DB_PASSWORD=$DB_PASSWORD
JWT_SECRET=$JWT_SECRET
JWT_EXPIRATION=86400000
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=$MAIL_USERNAME
MAIL_PASSWORD=$MAIL_PASSWORD
AWS_ACCESS_KEY=$AWS_ACCESS_KEY
AWS_SECRET_KEY=$AWS_SECRET_KEY
AWS_REGION=$AWS_REGION
AWS_BUCKET_NAME=$AWS_BUCKET_NAME
SERVER_PORT=$SERVER_PORT
EOF
                    '''
                }
            }
        }

        stage('Run Container') {
            steps {
                sh '''
                    docker run -d \
                      --name fullstack-container \
                      -p 9090:9090 \
                      --env-file app.env \
                      fullstack-backend
                '''
            }
        }
    }
}
