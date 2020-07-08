# Cloud Run Demo 2 - Rollout Updates

This demo explains how application updates can be rolled out without taking down time:

## Steps to Follow

1. Update hello world message in the `server.js` file created in [Code Run & Build](../1-code-run-build) demo:

   ```js
   const http = require('http');
   const server = http.createServer((req, res) => {
       console.log('Request received: [url] ' + req.url);
       res.end('Hello world from Cloud Run!');
   });

   const PORT = process.env.PORT || 8080;
   server.listen(PORT, () => { console.log('Listening on port ' + PORT);});
   ```

2. Rollout above update:

   ```bash
   project_id=gdg-cloud-run # GCP project id
   region=australia-southeast1 # Cloud run region
   gcloud run --platform managed --region ${region} deploy --image gcr.io/${project_id}/cloud-run-hello
   ```

3. Verify the update of the web application using the `Web Preview` option on the Cloud Shell.

## References

- [gcloud run Command Reference](https://cloud.google.com/sdk/gcloud/reference/run)