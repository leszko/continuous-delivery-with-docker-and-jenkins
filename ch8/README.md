# Chapter 8: Continuous Delivery Pipeline

All the instructions assumes that you have:
* Jenkins instance (with Java 8, Docker, and kubectl installed on Jenkins agents)
* Docker registry (e.g. account on Docker Hub)
* Two Kubernetes clusters (one available as the context `staging`, the other as `production`)
* Build Timestamp Plugin installed in Jenkins (and timestamp format configured to contain no whitespaces)
* Credentials for Docker Hub set in Jenkins

## Code Samples

### Code Sample 1: Complete Continuous Delivery pipeline

The [sample1](sample1) includes a Calculator project with the complete Continuous Delivery pipeline defined in `Jenkinsfile`.