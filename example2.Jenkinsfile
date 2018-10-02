// https://docs.okd.io/latest/using_images/other_images/jenkins.html#using-the-jenkins-kubernetes-plug-in
// Example 1. Example BuildConfig using the Jenkins Kubernetes Plug-in
kind: List
apiVersion: v1
items:
- kind: ImageStream
  apiVersion: v1
  metadata:
    name: openshift-jee-sample
- kind: BuildConfig
  apiVersion: v1
  metadata:
    name: openshift-jee-sample-docker
  spec:
    strategy:
      type: Docker
    source:
      type: Docker
      dockerfile: |-
        FROM openshift/wildfly-101-centos7:latest
        COPY ROOT.war /wildfly/standalone/deployments/ROOT.war
        CMD $STI_SCRIPTS_PATH/run
      binary:
        asFile: ROOT.war
    output:
      to:
        kind: ImageStreamTag
        name: openshift-jee-sample:latest
- kind: BuildConfig
  apiVersion: v1
  metadata:
    name: openshift-jee-sample
  spec:
    strategy:
      type: JenkinsPipeline
      jenkinsPipelineStrategy:
        jenkinsfile: |-
          node("maven") {
            stage('buildImage'){
                sh "git clone https://github.com/openshift/openshift-jee-sample.git ."
                sh "mvn -B -Popenshift package"
                sh "oc start-build -F openshift-jee-sample-docker --from-file=target/ROOT.war"
            }
        }
        node {
            stage('deployApplication'){
            openshiftDeploy(deploymentConfig: 'openshift-jee-sample')
            }
        }
    triggers:
    - type: ConfigChange


