# gae

1. Use the new Docker for Mac https://docs.docker.com/docker-for-mac/

2. Use whatever docker image makes sense (google provide some with all the tools installed). The one above looks good, I'm using:

FROM google/cloud-sdk

RUN apt-get update && apt-get install -y --no-install-recommends \
		g++ \
		gcc \
		libc6-dev \
		make \
    git \
    golang \
	&& rm -rf /var/lib/apt/lists/*

ENV PATH=${PATH}:/google-cloud-sdk/platform/google_appengine

RUN chmod +x /google-cloud-sdk/platform/google_appengine/goapp
RUN mkdir -p /go
ENV GOPATH /go

RUN go get google.golang.org/appengine/...

EXPOSE 8080 8000

3. Build the docker image: docker build . -t gae

4. Use vendoring so all your app files can be mapped easily but ignore the packages:

- golang.org/x/net/context
- google.golang.org/appengine

5. In app.yaml, set the vendor file to *not* be built:

nobuild_files:
- vendor

6. Run the docker image:

docker run --rm -v $(pwd):/app -p 8000:8000 -p 8080:8080 -ti gae

7. Use "goapp serve -host 0.0.0.0" to start the app

8. Access via localhost:8080 in the host browser (which Docker for Mac makes easier)
