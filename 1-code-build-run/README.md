# Cloud Run Demo 1 - Code, Build & Run

This demo illustrates how a web application can be implemented using Node.js, build and run it on Cloud Run using Docker.

## Steps to Follow

1. Open [Cloud Shell Editor](https://ssh.cloud.google.com/cloudshell/editor).

2. Create a folder with the name `cloud-run-hello` and create a file with the name `server.js` with the following content:

   ```js
   const http = require('http');
   const server = http.createServer((req, res) => {
       console.log('Request received: ' + req.url);
       res.end('Hello world!');
   });

   const PORT = process.env.PORT || 8080;
   server.listen(PORT, () => { console.log('Listening on port ' + PORT);});
   ```

3. Click on the `Web Preview` button on the Cloud Shell UI and open above listener on the web browser.

4. Create a `Dockerfile` with the following content for packaging above web application in a Docker image:

   ```
   FROM node:12-slim
   ADD server.js /app/
   WORKDIR app/
   CMD node server.js
   ```

5. Run the Docker image and verify the web application using the `Web Preview` option:

   ```bash
   docker run -p 8080:8080 -it cloud-run-hello
   ```

6. Push the Docker image to the `Container Registry`:

   ```
   project_id=gdg-cloud-run # GCP project id
   docker tag cloud-run-hello gcr.io/${project_id}/cloud-run-hello
   docker push gcr.io/${project_id}/cloud-run-hello
   ```

7. Deploy web application on `Cloud Run`:

   ```bash
   region="australia-southeast1" # Cloud run region
   gcloud run --platform managed --region ${region} deploy --image gcr.io/${project_id}/cloud-run-hello
   ```

## References:
- [Cloud Run](https://cloud.google.com/run)
- [Cloud Run Quickstart](https://cloud.google.com/run/docs/quickstarts/build-and-deploy)
- [Container Registry](https://cloud.google.com/container-registry)
