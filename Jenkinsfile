pipeline{
  agent none

  stages {
    stage ('Unit Tests') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f test.xml -v'
        junit 'reports/result.xml'
      }
    }
    stage('build') {
      agent {
        label 'apache'
      }
      steps {
        sh 'ant -f build.xml -v'
      }
      post {
        success {
          archiveArtifacts artifacts: 'dist/*.jar', fingerprint: true
        }
      }
    }
    stage ('deploy') {
      agent {
        label 'apache'
      }
      steps {
        sh "mkdir /var/www/html/rectangles/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangles/all/${env.BRANCH_NAME}/"
      }
    }
    stage ('Running on CentOS') {
      agent {
        label 'CentOS'
        }
      steps {
        sh "wget http://darshan275921.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }

    stage ('Test on Debian') {
      agent {
        docker 'openjdk:8u151-jre'
      }
      steps {
        sh "wget http://darshan275921.mylabserver.com/rectangles/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 3 4"
      }
    }
    stage ('Promote to Green') {
      agent {
        label 'apache'
      }
      when {
        branch 'master'
      }
      steps {
        sh "cp  /var/www/html/rectangles/all/rectangle_${env.BUILD_NUMBER}.jar  /var/www/html/rectangles/green/rectangle_${env.BUILD_NUMBER}.jar"
      }
    }
    stage ('Promote Develop Branch to Master') {
      agent {
        label 'apache'
      }
      when {
        branch 'develop'
      }
      steps {
        echo "stashing Any Local changes"
        sh 'git stash'
        echo "checking out Develop branch"
        sh 'git checkout develop'
        echo "checking out Master branch"
        sh 'git checkout master'
        echo "Merging develop into master branch"
        sh 'git merge develop'
        echo "pushing to origin Master"
        sh 'git push origin master'

      }
    }

  }
}
