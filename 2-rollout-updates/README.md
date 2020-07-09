# Cloud Run Demo 2 - Rollout Updates

This demo explains how application updates can be rolled out without taking down time:

## Steps to Follow

1. Update hello world message in the `server.js` file created in [Code, Build & Run](../1-code-build-run) demo:

   ```js
   const http = require('http');
   const server = http.createServer((req, res) => {
       console.log('Request received: [url] ' + req.url);
       res.end('Hello world from Cloud Run!');
   });

   const PORT = process.env.PORT || 8080;
   server.listen(PORT, () => { console.log('Listening on port ' + PORT);});
   ```

2. Build, tag and push Docker image to the Container Registry:

   ```bash
   docker build -t cloud-run-hello .
   docker tag cloud-run-hello gcr.io/${PROJECT_ID}/cloud-run-hello
   docker push gcr.io/${PROJECT_ID}/cloud-run-hello
   ```
   
3. Rollout above application update:

   ```bash
   PROJECT_ID="gdg-cloud-run" # GCP project id
   REGION="australia-southeast1" # Cloud run region

   gcloud run --platform managed --region ${REGION} deploy --image gcr.io/${PROJECT_ID}/cloud-run-hello
   ```

4. Verify the update of the web application using the `Web Preview` option on the Cloud Shell.

## References

- [gcloud run Command Reference](https://cloud.google.com/sdk/gcloud/reference/run)
