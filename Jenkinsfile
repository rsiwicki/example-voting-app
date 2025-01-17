pipeline {
  agent none
  stages {
    stage('worker build') {
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }

      }
      when {
        changeset '**/worker/**'
      }
      steps {
        dir(path: 'worker') {
          sh 'mvn compile'
        }

      }
    }

    stage('worker test') {
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }

      }
      when {
        changeset '**/worker/**'
      }
      steps {
        dir(path: 'worker') {
          sh 'mvn clean test'
        }

      }
    }

    stage('worker package') {
      agent {
        docker {
          image 'maven:3.6.1-jdk-8-alpine'
          args '-v $HOME/.m2:/root/.m2'
        }

      }
      when {
        branch 'master'
        changeset '**/worker/**'
      }
      steps {
        dir(path: 'worker') {
          sh 'mvn package -DskipTests'
          archiveArtifacts(artifacts: '**/target/*.jar', fingerprint: true)
        }

      }
    }

    stage('worker-docker-package') {
      agent any
      when {
        changeset '**/worker/**'
        branch 'master'
      }
      steps {
        echo 'worker app with docker start enhanced'
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin'){
            def workerImage = docker.build("robrockdataio/worker:v${env.BUILD_ID}","./worker")
            workerImage.push()
            workerImage.push("${env.BRANCH_NAME}")
          }
        }

      }
    }

    stage('result build') {
      agent {
        docker {
          image 'node:8.16.0-alpine'
        }

      }
      when {
        changeset '**/result/**'
      }
      steps {
        dir(path: 'result') {
          sh 'npm install'
        }

      }
    }

    stage('result test') {
      agent {
        docker {
          image 'node:8.16.0-alpine'
        }

      }
      when {
        changeset '**/result/**'
      }
      steps {
        dir(path: 'result') {
          sh 'npm install'
          sh 'npm test'
        }

      }
    }

    stage('result-docker-package') {
      agent any
      when {
        changeset '**/result/**'
        branch 'master'
      }
      steps {
        echo 'worker app with docker'
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin'){
            def workerImage = docker.build("robrockdataio/result:v${env.BUILD_ID}","./result")
            workerImage.push()
            workerImage.push("${env.BRANCH_NAME}")
          }
        }

      }
    }

    stage('vote build') {
      agent {
        docker {
          image 'python:2.7.16-slim'
          args '--user root'
        }

      }
      when {
        changeset '**/vote/**'
      }
      steps {
        dir(path: 'vote') {
          sh 'pip install -r requirements.txt'
        }

      }
    }

    stage('vote test') {
      agent {
        docker {
          image 'python:2.7.16-slim'
          args '--user root'
        }

      }
      when {
        changeset '**/vote/**'
      }
      steps {
        dir(path: 'vote') {
          sh 'echo hello'
        }

      }
    }

    stage('vote integration') {
      agent any 
      when {
        changeset '**/vote/**'
	branch 'master''
      }
      steps {
        dir(path: 'vote') {
           sh 'integration_test.sh'
        }

      }
    }


    stage('vote-docker-package') {
      agent any
      when {
        changeset '**/vote/**'
        branch 'master'
      }
      steps {
        echo 'voter app with docker'
        script {
          docker.withRegistry('https://index.docker.io/v1/', 'dockerlogin'){
            def workerImage = docker.build("robrockdataio/vote:v${env.BUILD_ID}","./vote")
            workerImage.push()
            workerImage.push("${env.BRANCH_NAME}")
          }
        }

      }
    }

    stage('Deploy to Dev') {
      agent any
      when {
        branch 'master'
      }
      steps {
        sh 'docker-compose up -d'
      }
    }

  }
  post {
    always {
      echo 'This pipeline for app instavote is completed now dockerized >-) >-) >-)'
    }

  }
}
