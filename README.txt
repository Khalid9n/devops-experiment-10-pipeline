DEVOPS EXPERIMENT 10 — END-TO-END DEVOPS PIPELINE
Student: Khalid Shaikh | Roll No.: 241453 | Batch: B

PIPELINE STAGES
1. Checkout: Jenkins checks out the configured GitHub repository.
2. Install: npm ci installs dependencies from package-lock.json.
3. Test: npm test runs Node.js built-in automated tests.
4. Docker Build: creates build-number and latest image tags.
5. Docker Hub Push: authenticates with Jenkins credentials and pushes both tags.
6. Deploy: replaces the named app container and publishes port 3000.
7. Smoke Test: curl checks http://localhost:3000/health.

LOCAL CHECK BEFORE CONNECTING JENKINS
1. Install Node.js 20+ and Docker Desktop, and start Docker Desktop.
2. Open a terminal in this folder.
3. Run: npm ci
4. Run: npm test
5. Run: npm start
6. Open http://localhost:3000 and http://localhost:3000/health
7. Stop the local app with Ctrl+C before a deployment takes port 3000.

GITHUB
Keep your existing .git folder when copying these files over an existing repository.
Commit and push the files to the existing main branch.

DOCKER HUB AND JENKINS
1. Create a Docker Hub repository named devops-pipeline-app.
2. Edit Jenkinsfile and replace YOUR_DOCKERHUB_USERNAME with your Docker Hub username.
3. In Jenkins > Manage Jenkins > Credentials, create a Username with password credential:
   ID: dockerhub-creds
   Username: your Docker Hub username
   Password: a Docker Hub access token (do not put the token in source code).
4. Install/verify Jenkins plugins: Git, Pipeline, and Credentials Binding.
5. Create a Pipeline job configured as Pipeline script from SCM:
   SCM: Git
   Repository URL: https://github.com/Khalid9n/devops-pipeline-experiment10.git
   Branch specifier: */main
   Script Path: Jenkinsfile
6. Jenkins must be able to run node, npm, docker and curl.exe under its service account.
7. The Docker Desktop engine must be reachable from the Jenkins service account.
8. The pipeline polls SCM about every two minutes. Polling only works after the job has completed its initial configuration/build.

SCREENSHOTS TO CAPTURE FROM ACTUAL RUNS
A. npm test showing both tests pass.
B. GitHub repository with the project files.
C. Jenkins job configuration showing Pipeline from SCM and */main.
D. Jenkins Stage View showing the seven stages.
E. Console output for successful tests and Docker build.
F. Docker Hub repository with build-number and latest tags.
G. docker ps showing the app container mapped to port 3000.
H. Browser/curl output from /health showing {"status":"UP"}.

SAFETY / LIMITATIONS
- Do not commit Docker Hub passwords/tokens or .env files.
- Docker Hub push/deploy requires valid credentials, Docker Desktop running, and the repository name to match.
- A failed earlier stage prevents later stages from running.
- This starter uses local deployment on the same Windows machine; it is not a public internet deployment.
