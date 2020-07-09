# Cloud Run Demo 1 - Code, Build & Run

This demo illustrates how a web application can be implemented using Node.js, build and run it on Cloud Run using Docker.

## Steps to Follow

1. Open [Cloud Shell Editor](https://ssh.cloud.google.com/cloudshell/editor).

2. Create a folder with the name `cloud-run-hello` and create a file with the name `server.js` with the following content:

   ```js
   const http = require('http');
   const server = http.createServer((req, res) => {
       console.log('Request received: [url] ' + req.url);
       res.end('Hello world!');
   });

   const PORT = process.env.PORT || 8080;
   server.listen(PORT, () => { console.log('Listening on port ' + PORT);});
   ```

3. Start above web application on the `Cloud Shell Terminal`:

   ```bash
   node server.js
   ```

4. Click on the `Web Preview` button on the Cloud Shell UI and open above HTTP listener on the web browser.

5. Create a `Dockerfile` with the following content for packaging above web application in a Docker image:

   ```bash
   FROM node:12-slim
   ADD server.js /app/
   WORKDIR app/
   CMD node server.js
   ```

6. Build the Docker image:

   ```bash
   docker build -t cloud-run-hello .
   ```
   
7. Run the Docker image and verify the web application using the `Web Preview` option:

   ```bash
   docker run -p 8080:8080 -it cloud-run-hello
   ```

8. Push the Docker image to the `Container Registry`:

   ```bash
   PROJECT_ID="gdg-cloud-run" # GCP project id

   docker tag cloud-run-hello gcr.io/${PROJECT_ID}/cloud-run-hello
   docker push gcr.io/${PROJECT_ID}/cloud-run-hello
   ```

9. Deploy web application on `Cloud Run`:

   ```bash
   REGION="us-east1" # Cloud run region
   
   gcloud config set project ${PROJECT_ID}
   gcloud run deploy cloud-run-hello --platform managed --region ${REGION} --image gcr.io/${PROJECT_ID}/cloud-run-hello --allow-unauthenticated
   ```

## References:
- [Cloud Run](https://cloud.google.com/run)
- [Cloud Run Quickstart](https://cloud.google.com/run/docs/quickstarts/build-and-deploy)
- [Container Registry](https://cloud.google.com/container-registry)
