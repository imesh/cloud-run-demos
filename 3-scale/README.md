# Cloud Run Demo 3 - Scale

This demo illustrates how Cloud Run applications scale according to incoming request load.

## Steps to Follow

1. Sign up and sign in in [loader.io](https://loader.io) cloud based load testing tool.

2. Create a Loader test pointing to the `cloud-run-hello` HTTP endpoint created in [Code, Build & Run](../1-code-build-run) demo and obtain a target endpoint verification token.

3. Add Loader target verification token ({token}) in server.js file in `cloud-run-hello` web application. 

   ```js
   const http = require('http');
   const server = http.createServer((req, res) => {
       console.log('Request received: [url]' + req.url);
       // Used by loader.io for target endpoint verfication
       if(req.url === '/loaderio-{token}/') {
           res.end('loaderio-{token}');
       } else {
           res.end('Hello world from Cloud Run!');
       }
   });

   const PORT = process.env.PORT || 8080;
   server.listen(PORT, () => {
       console.log('Server listening on ' + PORT);
   });
   ```

4. Build and push Docker image to the Container Registry:

   ```bash
   docker build -t gcr.io/${PROJECT_ID}/cloud-run-hello .
   docker push gcr.io/${PROJECT_ID}/cloud-run-hello
   ```
   
5. Rollout above application update:

   ```bash
   PROJECT_ID="gdg-cloud-run" # GCP project id
   REGION="us-east1" # Cloud run region

   gcloud run deploy cloud-run-hello --platform managed --region ${REGION} --image gcr.io/${PROJECT_ID}/cloud-run-hello --allow-unauthenticated
   ```
6. Generate a HTTP request load using the Loader test created and analyze the graph.

## References

- [Cloud Run Container Instance Autoscaling](https://cloud.google.com/run/docs/about-instance-autoscaling)
- [Creating a Test in Loader](https://support.loader.io/article/15-creating-a-test)

