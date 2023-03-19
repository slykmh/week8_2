podTemplate(yaml: '''
    apiVersion: v1
    kind: Pod
    spec:
        containers:
        - name: gradle
          image: gradle:jdk8
          command:
          - sleep
          args:
          - 99d
          restartPolicy: Never
    '''){
node(POD_LABEL) {
    stage('Kubernetes on gradle container') {
    git 'https://github.com/slykmh/Continuous-Delivery-with-Docker-and-Jenkins-Second-Edition.git'
    container('gradle') {
      stage('start calculator') {
        sh '''
        cd Chapter08/sample1
        curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
        chmod +x ./kubectl
        ./kubectl apply -f calculator.yaml
        ./kubectl apply -f hazelcast.yaml
        '''
        }
      stage('testing'){
          try {
                sh '''
                cd Chapter09/sample3
                chmod +x gradlew
                ./gradlew acceptanceTest -Dcalculator.url=http://calculator-service:8080
                '''
                publishHTML (target: [
                  reportDir: 'sample3/build/reports/AcceptanceTest',
                  reportFiles: 'main.html',
                  reportName: "Acceptance Test"
                ]) 
               } catch (Exception E) {
                  echo 'Failure detected'
          }
      }
    }
  }
}
}
