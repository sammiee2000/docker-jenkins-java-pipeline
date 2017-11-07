node {
  checkout scm
  env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"
  stage('Package') {
    dir('webapp') {
      sh 'mvn clean package -DskipTests'
    }
  }

  stage('Create Docker Image') {
    dir('webapp') {
      //docker.build("sarataha/docker-jenkins-java-pipeline:${env.BUILD_NUMBER}").push()
      docker.build("sarataha/docker-jenkins-java-pipeline:${env.BUILD_NUMBER}")
    }
  }

  stage ('Run Application') {
    //sh 'docker-compose run -d --name db --service-ports db'
    sh "docker-compose run -e DB_URI=`docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' db` -e BUILD_NUMBER=${env.BUILD_NUMBER} app2"
    //sh 'docker-compose stop db'
    //sh 'docker-compose rm db'
    //dir ('webapp') {
    //  sh 'mvn exec:java -DskipTests'
    //}
  }

  stage('Run Tests') {
    //db = docker.image("sarataha/couchbase").withRun("-d --name db --service-ports db")
    dir('webapp') {
      //sh "mvn test -DDB_URI=`docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' db`"
      sh "mvn test"
    }
    //db.stop()
    junit '**/target/surefire-reports/*.xml'
  }
}