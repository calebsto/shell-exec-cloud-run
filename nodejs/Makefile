PROJECT_ID=$(shell gcloud config get-value core/project)
all:
	@echo "build  - Build the docker image"
	@echo "deploy - Deploy the image to Cloud Run"
	@echo "clean  - Clean resoruces created in this test"
	@echo "call   - Call the Cloud Run service"

deploy:
	gcloud run deploy asu \
		--image gcr.io/$(PROJECT_ID)/asu \
		--min-instances 999 \
		--max-instances 1000 \
		--cpu 4 \
		--memory 8Gi \
		--platform managed \
		--region us-central1 \
		--allow-unauthenticated \
		--timeout 10m

build:
	gcloud builds submit --tag gcr.io/$(PROJECT_ID)/asu

clean:
	-gcloud container images delete gcr.io/$(PROJECT_ID)/cloud-run-exec-nodejs --quiet
	-gcloud run services delete cloud-run-exec-nodejs \
		--platform managed \
		--region us-central1 \
		--quiet

call:
	@echo "Calling Node-JS Cloud Run service"
	@url=$(shell gcloud run services describe cloud-run-exec-nodejs --format='value(status.url)' --region us-central1 --platform managed); \
	token=$(shell gcloud auth print-identity-token); \
	curl --request POST \
  		--header "Authorization: Bearer $$token" \
  		--header "Content-Type: text/plain" \
  		$$url/exec \
  		--data-binary "ps -ef"
