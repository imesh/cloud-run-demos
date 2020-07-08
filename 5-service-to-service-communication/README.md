# Cloud Run Demo 5 - Service to Service Communication

This demo shows how service to service integrations can be implemented while providing service level authentication using Cloud IAM (service accounts).

## Steps to Follow

1. Create two service accounts for `cloud-run-hello-api` and `cloud-run-hello-web-app` applications:

   ```bash
   gcloud iam service-accounts create cloud-run-hello-api
   gcloud iam service-accounts create cloud-run-hello-web-app
   ```

2. Go to [Cloud Build](https://console.cloud.google.com/cloud-build/) UI and connect [cloud-run-hello-api Github repository](https://github.com/imesh/cloud-run-hello-api) to Cloud Build and create a [Push Trigger](https://console.cloud.google.com/cloud-build/triggers).

3. Manually run the `Push Trigger`, verify the build and find the HTTP endpoint URL (refer this as HELLO_API_URL).

4. Obtain an access token using gcloud CLI and verify `cloud-run-hello-api`:

   ```bash
   HELLO_API_URL=#cloud-run-hello-api service URL
   TOKEN=$(gcloud auth print-identity-token)

   curl -H "Authorization: Bearer $TOKEN" ${HELLO_API_URL}
   ```

   Note: Above `gcloud auth print-identity-token` will use compute default service account for obtaining an access token. Hence, it will provide permissions for accessing any Cloud Run service in the given GCP account.

5. Set `HELLO_API_URL` value in [cloudbuild.yaml](https://github.com/imesh/cloud-run-hello-web-app/blob/master/cloudbuild.yaml) in [cloud-run-hello-web-app Github repository](https://github.com/imesh/cloud-run-hello-web-app).

6. Go to [Cloud Build](https://console.cloud.google.com/cloud-build/) UI and connect [cloud-run-hello-web-app Github repository](https://github.com/imesh/cloud-run-hello-web-app) to Cloud Build and create a [Push Trigger](https://console.cloud.google.com/cloud-build/triggers).

7. Manually run the push trigger and access the `cloud-run-hello-web-app` HTTP endpoint URL using the `Web Preview` option on the Cloud Shell. If everything is executed correctly, the below error should be raised:

  ```
  Error invoking hello-api: Error: 403 Forbidden
  ```

8. Now, grant permissions for `cloud-run-hello-web-app` to access `cloud-run-hello-api` by adding an IAM policy binding to the `cloud-run-hello-api`:

   ```bash
   REGION="australia-southeast1"
   RECEIVING_SERVICE="cloud-run-hello-api"
   CALLING_SERVICE_IDENTITY="cloud-run-hello-web-app@gdg-cloud-run.iam.gserviceaccount.com"

   gcloud run services add-iam-policy-binding ${RECEIVING_SERVICE} \
     --member="serviceAccount:${CALLING_SERVICE_IDENTITY}" \
     --role='roles/run.invoker' --platform managed --region ${REGION}
   ```

9. Re-deploy `cloud-run-hello-web-app` to refresh its state:

   ```bash
   REGION="australia-southeast1"
   HELLO_API_URL=#cloud-run-hello-api service URL

   gcloud run deploy cloud-run-hello-web-app --platform managed --region ${REGION} --image gcr.io/gdg-cloud-run/cloud-run-hello-web-app:latest --set-env-vars HELLO_API_URL=${HELLO_API_URL} --service-account cloud-run-hello-web-app --allow-unauthenticated
   ```

10. Access `cloud-run-hello-web-app` HTTP endpoint URL. Now, the response sent by `cloud-run-hello-api` should be returned to the client by `cloud-run-hello-web-app`:

   ```json
   {"hello":"api"}
   ```

## References

- [Easily generate Google signed id-token with token-generator](https://medium.com/google-cloud/easily-generate-google-signed-id-token-with-token-generator-d25b7e235f2e)
- [Securing Cloud Run services tutorial](https://cloud.google.com/run/docs/tutorials/secure-services)
- [Authenticating service-to-service](https://cloud.google.com/run/docs/authenticating/service-to-service)
