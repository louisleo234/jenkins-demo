node('haimaxy-jnlp') {
    stage('Clone') {
        echo "1. Clone Stage"
        git url: "https://github.com/cnych/jenkins-demo.git"
        script {
            build_tag = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
        }
    }
    stage('Test') {
        echo "2. Test Stage"
    }
    stage('Build') {
        echo "3. Build Docker Image Stage"
        sh "docker build -t louisleo234/jenkins-demo:${build_tag} ."
    }
    stage('Push') {
        echo "4. Push Docker Image Stage"
        withCredentials([usernamePassword(credentialsId: 'dockerHub', usernameVariable: 'username', passwordVariable: 'password')]) {
        sh "docker login -u ${username} -p ${password}"
        sh "docker push louisleo234/jenkins-demo:${build_tag}"
        }
    }
    stage('deploy') {
        echo "5. Change YAML FILE Stage"
        def userInput = input(
            id: 'userInpunt',
            message: 'Choose a deploy env',
            parameters: [
                [
                    $class: 'ChoiceParameterDefinition',
                    choices: 'Dev\nQA\nProd',
                    name: "Env"
                ]
            ]
        )
        echo "This is a deploy step to ${userInput} env"
        sh "sed -i 's/<BUILD_TAG>/${build_tag}/' k8s.yaml"
        sh "sed -i 's/<BRANCH_NAME>/${env.BRANCH_NAME}/' k8s.yaml"
        if (userInput == "Dev") {
            // deploy dev stuff
        } else if (userInput == "QA"){
            // deploy qa stuff
        } else {
            // deploy prod stuff
        }
        sh "kubectl apply -f k8s.yaml --record"
    }
}
