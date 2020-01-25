node {
def inputParams = inputParamsString(new File(pwd()))

// Change `message` value to the message you want to display
// Change `description` value to the description you want
def selectedProperty = input( id: 'userInput', message: 'Choose properties file', parameters: [ [$class: 'ChoiceParameterDefinition', choices: inputParams, description: 'Properties', name: 'prop'] ])

println "Property: $selectedProperty"

}

import static groovy.io.FileType.FILES

@NonCPS
def inputParamsString(dir) {
    def list = []

    // If you don't want to search recursively then change `eachFileRecurse` -> `eachFile`
    dir.eachFileRecurse(FILES) {
        // Change `.properties` to the file extension you are interested in
        if(it.name.endsWith('.md')) {
            // If the full path is required remove `.getName()`
            list << it.getName()
        }
    }

    list.join("\n")
}

pipeline {
    agent any

    options {
        timestamps()
        ansiColor("xterm")
    }

    stages {
        stage('My Stage') {
            steps {
                script {
                    def GIT_TAGS = sh (script: 'git tag -l', returnStdout:true).trim()
                    inputResult = input(
                        message: "Select a git tag",
                        parameters: [choice(name: "git_tag", choices: "${GIT_TAGS}", description: "Git tag")]
                    )
                }
            }
        }

        stage('My other Stage') {
            steps{
                echo "The selected tag is: ${inputResult}"
            }
        }

        stage("Build") {
            options {
                timeout(time: 1, unit: "MINUTES")
            }
            steps {
                sh 'printf "\\e[31mSome code compilation here...\\e[0m\\n"'
                script {
                    env.RELEASE_SCOPE = input message: 'User input required', ok: 'Release!',
                                        parameters: [
                                        [choice(name: 'RELEASE_SCOPE0', choices: 'patch\nminor\nmajor', description: 'What is the release scope?')],
                                        [choice(name: 'RELEASE_SCOPE1', choices: 'patch\nronim\rojam', description: 'What is the release eposc?')],
                                        ]
                }
                echo "${env.RELEASE_SCOPE['RELEASE_SCOPE0']}"
            }
        }

        stage("Test") {
            when {
                environment name: "FOO", value: "bar"
            }
            options {
                timeout(time: 2, unit: "MINUTES")
            }
            steps {
                sh 'printf "\\e[31mSome tests execution here...\\e[0m\\n"'
            }
        }
    }
}
