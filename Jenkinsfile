pipeline {
	agent any
     triggers {
          pollSCM('H/20 * * * *')
     }
     stages {
          stage("Compile Java") {
		  echo "I am from master branch"
		steps {
		   git url: "https://github.com/Yaash19/week6" , branch: "master"
           sh "chmod +x gradlew"
		   sh "./gradlew compileJava"
               }
          }
          stage("Unit test") {
		steps {
                    sh "./gradlew test"
               }
          }
          stage("Code coverage") {
		  echo "I am from master branch"
          	       steps {
                              sh "./gradlew jacocoTestReport"
                              sh "./gradlew jacocoTestCoverageVerification"
                         }
                    }
          stage("Static code analysis") {
		  echo "I am from master branch"
          		steps {
                              sh "./gradlew checkstyleMain"
                         }
                    }
                    stage("Package") {
					echo "I am from master branch"
          		steps {
                              sh "./gradlew build"
                         }
                    }
     }
  }

podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
      containers:
      - name: gradle
        image: gradle:6.3-jdk14
        command:
        - sleep
        args:
        - 99d
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt
      - name: kaniko
        image: gcr.io/kaniko-project/executor:debug
        command:
        - sleep
        args:
        - 9999999
        volumeMounts:
        - name: shared-storage
          mountPath: /mnt
        - name: kaniko-secret
          mountPath: /kaniko/.docker
      restartPolicy: Never
      volumes:
      - name: shared-storage
        persistentVolumeClaim:
          claimName: jenkins-pv-claim
      - name: kaniko-secret
        secret:
            secretName: dockercred
            items:
            - key: .dockerconfigjson
              path: config.json
''')

{
  node(POD_LABEL)
	{
    		stage('Build a gradle project')
			echo "I am from master branch"
		{
     		git url: "https://github.com/Yaash19/week6" , branch: "master"
     		container('gradle')
			{
        		stage('Build container')
				{
         	 		sh '''
                    chmod +x gradlew
                    ./gradlew build
                    mv ./build/libs/calculator-0.0.1-SNAPSHOT.jar /mnt
                    '''
				}
			}
		}
		stage('Build Image')
		echo "I am from master branch"
		{
			container('kaniko')
			{
        		stage('Build a calculator program')
				echo "I am from master branch"
				{
          			sh '''
					echo 'FROM openjdk:8-jre' > Dockerfile
					echo 'COPY ./calculator-0.0.1-SNAPSHOT.jar app.jar' >> Dockerfile
					echo 'ENTRYPOINT ["java", "-jar", "app.jar"]' >> Dockerfile
					mv /mnt/calculator-0.0.1-SNAPSHOT.jar .
					/kaniko/executor --context `pwd` --destination narendocker123/calculator:1.0
					'''
				}
			}
		}
	}
}