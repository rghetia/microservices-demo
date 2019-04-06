FROM golang:1.11.4-alpine as builder

RUN apk update && apk upgrade \
    && apk add --no-cache ca-certificates \
    && update-ca-certificates \
    && apk add --no-cache bash git openssh gcc musl-dev bzr build-base

RUN mkdir -p /go/src/github.com/census-instrumentation
WORKDIR /go/src/github.com/census-instrumentation
RUN git clone https://github.com/census-instrumentation/opencensus-service.git

WORKDIR /go/src/github.com/census-instrumentation/opencensus-service
RUN GO111MODULE=on go mod download

# This to trigger forced rebuild from here.
COPY ./Dockerfile /Dockerfile
RUN git fetch origin
RUN git checkout eef0179c88837e393e217a40e836b8f8edc52d9f
RUN rm -rf /go/src/github.com/census-instrumentation/opencensus-service/go.sum
RUN git log -10 > /gitlog.txt
RUN make agent && cp ./bin/ocagent_linux /ocagent_linux

FROM alpine as release

RUN apk update && apk upgrade && \
    apk add --no-cache bash git openssh curl

WORKDIR /

COPY --from=builder /ocagent_linux /ocagent_linux
COPY --from=builder /gitlog.txt /gitlog.txt
#COPY --from=builder ./config.yaml config.yaml
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

CMD ["/ocagent_linux"]
EXPOSE 55678 55679
