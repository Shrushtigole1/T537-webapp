pipeline{
agent any
environment{
REPOSITORY = 'webapp'
IMAGE_NAME = 'webapp'
IMAGE_TAG = "${BUILD_NUMBER}"
USERNAME = 'shrushtigole1'
}
stages{
stage("Checkout"){
steps{
checkout scm
}
}
stage("build"){
steps{
sh'''
               docker build -t ${IMAGE_NAME}:${IMAGE_TAG} .
               '''
}
}
        stage("DockerHub Login"){
            steps{
                sh '''
                echo "${DOCKER_PASSWORD}" | docker login -u "${USERNAME}" --password-stdin
                '''
            }
        }
        stage("Image Push"){
            steps{
                sh'''
                docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}
                docker push ${USERNAME}/${IMAGE_NAME}:${IMAGE_TAG}
                '''
        stage('DockerHub Login and Push') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-credentials',
                        usernameVariable: 'DOCKERHUB_USERNAME',
                        passwordVariable: 'DOCKERHUB_TOKEN'
                    )
                ]) {
                    sh '''
                        printf '%s' "$DOCKERHUB_TOKEN" | \
                          docker login -u "$DOCKERHUB_USERNAME" --password-stdin

                        docker tag $IMAGE_NAME:$IMAGE_TAG \
                          $DOCKERHUB_USERNAME/$IMAGE_NAME:$IMAGE_TAG

                        docker push \
                          $DOCKERHUB_USERNAME/$IMAGE_NAME:$IMAGE_TAG

                        docker logout
                    '''
                }
}
}
stage("Deploy"){