# Cloud Run Demo 4 - Set up CI/CD Integration

This demo explains how Continuous Integration can be set up using Cloud Build for applications deployed on Cloud Run.

## Steps to Follow

1. Create a Github repository and add `server.js` and `Dockerfile` created in [Code, Build & Run](../1-code-build-run) demo to repository's root folder.

   server.js:
   ```js
   const http = require('http');
   const server = http.createServer((req, res) => {
       console.log('Request received: [url] ' + req.url);
       res.end('Hello world from Cloud Run!');
   });

   const PORT = process.env.PORT || 8080;
   server.listen(PORT, () => { console.log('Listening on port ' + PORT);});
   ```

   Dockerfile:
   ```bash
   FROM node:12-slim
   ADD server.js /app/
   WORKDIR app/
   CMD node server.js
   ```

2. Create a `cloudbuild.yaml` file in the same Github repository with the following content:

   ```yaml
   steps:
     # build docker image
   - name: 'gcr.io/cloud-builders/docker'
     args: ['build', '-t', 'gcr.io/gdg-cloud-run/cloud-run-hello:$COMMIT_SHA', '.']
     # push docker image to container registry
   - name: 'gcr.io/cloud-builders/docker'
     args: ['push', 'gcr.io/gdg-cloud-run/cloud-run-hello:$COMMIT_SHA']
     # deploy application on cloud run
   - name: 'gcr.io/cloud-builders/gcloud'
     args: ['run' , 'deploy', 'cloud-run-hello', '--platform', 'managed', '--region', 'australia-southeast1', '--image', 'gcr.io/gdg-cloud-run/cloud-run-hello:$COMMIT_SHA', '--allow-unauthenticated']
   ```

4. Go to [Cloud Build](https://console.cloud.google.com/cloud-build/) UI and connect above Github repository to Cloud Build and create a [Push Trigger](https://console.cloud.google.com/cloud-build/triggers).

5. Manually run the Push Trigger and verify the build configuration.

6. Update the hello world message in `server.js` and commit the file:

   ```js
   const http = require('http');
   const server = http.createServer((req, res) => {
       console.log('Request received: [url] ' + req.url);
       res.end('Hello world from Cloud Run + CI/CD!');
   });

   const PORT = process.env.PORT || 8080;
   server.listen(PORT, () => { console.log('Listening on port ' + PORT);});
   ```

7. Verify the build in [Cloud Build](https://console.cloud.google.com/cloud-build/) UI.

8. If the build is successful, verify the update of the web application using the public endpoint URL.

## References
- [Cloud Build Configuration Overview](https://cloud.google.com/cloud-build/docs/build-config)
- [Building Container Images](https://cloud.google.com/cloud-build/docs/building/build-containers)
- [Creating Github App Triggers](https://cloud.google.com/cloud-build/docs/automating-builds/create-github-app-triggers)
