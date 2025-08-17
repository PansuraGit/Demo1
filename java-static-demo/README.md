# Java Static Frontend Demo (Git → Jenkins → Docker)

A minimal Java (Spring Boot) project that serves **static files** from `src/main/resources/static` and includes a ready-to-use **Jenkins CI/CD** pipeline and **Dockerfile**.

## What you get
- Spring Boot app that serves `/index.html` and `/styles.css`
- Multi-stage Dockerfile (small runtime image)
- Jenkinsfile (Checkout → Maven build/test → Docker build/push → optional deploy)
- Works on Docker Hub by default (change to ECR/GCR if needed)

## Quick start (local)
```bash
# Build & run without Docker
mvn -B -DskipTests package
java -jar target/staticapp-0.0.1-SNAPSHOT.jar
# Visit: http://localhost:8080
```

## Docker (local)
```bash
docker build -t java-static-demo:local .
docker run -d -p 8080:8080 --name java-static-demo java-static-demo:local
```

## Jenkins setup (high-level)
1. Create a **Pipeline** job pointing to this Git repo.
2. On the Jenkins agent, ensure Docker & Maven are available.
3. Add Jenkins **Credentials** (type: Username/Password) for Docker Hub with ID: `dockerhub-creds` (or change `Jenkinsfile`).
4. Update `IMAGE_NAME` in `Jenkinsfile` to your repo (e.g., `yourname/java-static-demo`).
5. Build the job. Image will be pushed as `:BUILD_NUMBER` and `:latest`.

## Switch to AWS ECR (optional)
- Replace the Push stage with an ECR login and push commands:
```groovy
sh 'aws ecr get-login-password | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.<region>.amazonaws.com'
sh 'docker tag ${IMAGE_NAME}:${BUILD_NUMBER} <aws_account_id>.dkr.ecr.<region>.amazonaws.com/${ECR_REPO}:${BUILD_NUMBER}'
sh 'docker push <aws_account_id>.dkr.ecr.<region>.amazonaws.com/${ECR_REPO}:${BUILD_NUMBER}'
```

## Project layout
```
.
├── Dockerfile
├── Jenkinsfile
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── example
│   │   │           └── staticapp
│   │   │               └── StaticAppApplication.java
│   │   └── resources
│   │       └── static
│   │           ├── index.html
│   │           └── styles.css
└── .dockerignore
```

## Notes
- Spring Boot automatically serves files from `src/main/resources/static` at `/`.
- Change the port by setting `server.port=9090` in `src/main/resources/application.properties`.
