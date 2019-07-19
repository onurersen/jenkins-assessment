**Jenkins Assessment**

Goal of this assessment is creating a CI / CD implementation for a simple Java/Maven project.

This is a working example - not a pseudo work.

---

**Toolstack of this project:**

* **Jenkins** was used to create CI/CD Pipeline.
* **Sonatype Nexus** was used for Artifact Repository.
* **Sonarqube** for Static Code Analysis (SAST).
* **Mailhog** is used to have a local SMTP server for email notifications (Simulation).

Development was done using below Docker images:

* **beratto/jenkins-lts-jdk8-maven** (in Docker Hub - I built this to have jdk,maven,git etc.)
* **sonatype/nexus3** (from Docker Hub)
* **sonarqube** (from Docker Hub)
* **mailhog/mailhog** (from Docker Hub)

**Snapshot of Containers spinned up for this assessment:**

| CONTAINER ID  | IMAGE | PORTS |
| ------------- | ------------- | ------------- |
| 1fe2e5b3c7b3  | sonarqube  | 0.0.0.0:9000->9000/tcp 
| 36e3fca993a4  | mailhog/mailhog  | 0.0.0.0:1025->1025/tcp, 0.0.0.0:8025->8025/tcp |
| 470095ed5479  | sonatype/nexus3  | 0.0.0.0:8081->8081/tcp |
| e201380f3986  | jenkins-lts-jdk8-maven:latest  | 0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp | 

--- 

**Additional plug-ins installed on Jenkins:**

* Email Extension Plugin (https://wiki.jenkins-ci.org/display/JENKINS/Email-ext+plugin)
* HTML Publisher plugin (http://wiki.jenkins-ci.org/display/JENKINS/HTML+Publisher+Plugin)
* JaCoco Plugin (https://wiki.jenkins.io/display/JENKINS/JaCoCo+Plugin)
* Nexus Artifact Uploader (https://wiki.jenkins-ci.org/display/JENKINS/Nexus+Artifact+Uploader)
* Pipeline Utility Steps Plugin (https://wiki.jenkins.io/display/JENKINS/Pipeline+Utility+Steps+Plugin)
* SonarQube Scanner for Jenkins Plugin (https://docs.sonarqube.org/latest/analysis/scan/sonarscanner-for-jenkins/)

To enable access to host machine from the docker containers I attached an IP alias to my network interface. 

All containers are communicating with each other through below IP address.

```
ifconfig lo0 alias 123.123.123.123/24
```
---

**Pipeline created for this assessment goes through following stages:**

1. Clone-Repo (from Github)
1. Build-Project (using Maven)
1. Execute-Tests (Unit Tests)
1. Code-Coverage (Code Coverage Test)
1. Sonarqube-Scan (Static Code Analysis)
1. Archive-Artifacts (Archiving .jar file(s))
1. Publish-Artifacts (Tranferring artifacts to Sonatype Nexus)

---

To share functionality across projects, Shared Libraries functionality on Jenkins was used.

Another project named **jenkins-assessment-shared-lib** was created for this purpose. By implementing this project shared functionalities can be created, maintained and versioned on demand.
