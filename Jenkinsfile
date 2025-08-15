pipeline {
    agent any

    environment {
        EC2_USER = 'ubuntu'
        EC2_HOST = '52.90.139.179'
        SSH_CRED_ID = '1c730f24-8821-4473-946d-e1e3aa149298'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git branch: 'main', url: 'https://github.com/sagar-rathod-devops/git-collaboration.git'
            }
        }

        stage('Package Application') {
            steps {
                sh 'zip -r app.zip *'
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent([env.SSH_CRED_ID]) {
                    sh """
                        echo "Uploading package to EC2..."
                        scp -o StrictHostKeyChecking=no app.zip ${EC2_USER}@${EC2_HOST}:/tmp/

                        echo "Deploying on EC2..."
                        ssh -o StrictHostKeyChecking=no ${EC2_USER}@${EC2_HOST} << 'EOF'
sudo apt-get update -y
sudo apt-get install -y unzip apache2
sudo rm -rf /var/www/html/*
sudo unzip -o /tmp/app.zip -d /var/www/html/
sudo chown -R www-data:www-data /var/www/html/
sudo systemctl restart apache2
EOF
                    """
                }
            }
        }
    }
}
