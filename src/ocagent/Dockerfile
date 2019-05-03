FROM golang:1.11.4-alpine as builder

RUN apk update && apk upgrade \
    && apk add --no-cache ca-certificates \
    && update-ca-certificates \
    && apk add --no-cache bash git openssh gcc musl-dev bzr build-base

RUN mkdir -p /go/src/github.com/census-instrumentation
WORKDIR /go/src/github.com/census-instrumentation
RUN git clone https://github.com/census-instrumentation/opencensus-service.git
WORKDIR /go/src/github.com/census-instrumentation/opencensus-service
RUN git checkout ddbbf72b5ebadbd1563aefd52932bf2b86022263
RUN GO111MODULE=on go mod download
# trigger new build v25
COPY Dockerfile .

RUN git remote add istio https://github.com/rghetia/opencensus-service.git
RUN git fetch istio
RUN git checkout istio_stats_reciever

RUN git log -n 11 > /git.log

RUN rm -rf /go/src/github.com/census-instrumentation/opencensus-service/go.sum
RUN make agent && cp ./bin/ocagent_linux /ocagent_linux

FROM alpine as release

RUN apk update && apk upgrade && \
    apk add --no-cache bash git openssh curl

WORKDIR /

COPY --from=builder /ocagent_linux /ocagent_linux
#COPY --from=builder ./config.yaml config.yaml
COPY --from=builder ./git.log git.log
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

CMD ["/ocagent_linux"]
EXPOSE 55678 55679 55700

