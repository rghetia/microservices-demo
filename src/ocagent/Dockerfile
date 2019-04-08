FROM golang:1.11.4-alpine as builder

RUN apk update && apk upgrade \
    && apk add --no-cache ca-certificates \
    && update-ca-certificates \
    && apk add --no-cache bash git openssh gcc musl-dev bzr build-base

RUN mkdir -p /go/src/github.com/census-instrumentation
WORKDIR /go/src/github.com/census-instrumentation
RUN git clone https://github.com/census-instrumentation/opencensus-service.git

WORKDIR /go/src/github.com/census-instrumentation/opencensus-service
RUN git checkout e6eac5a9d2ce4ed29585e7290ed191c3aa3f892d
RUN rm -rf /go/src/github.com/census-instrumentation/opencensus-service/go.sum
RUN GO111MODULE=on go mod download
RUN git remote add debug https://github.com/rghetia/opencensus-service.git
RUN git fetch debug
RUN git checkout vm_stats_units_debug
#RUN git fetch origin pull/520/head:pr-520
#RUN GO111MODULE=on CGO_ENABLED=0 go build -o ./bin/ocagent_linux $(BUILD_INFO) ./cmd/ocagent  && cp ./bin/ocagent_linux /ocagent_linux
RUN make agent && cp ./bin/ocagent_linux /ocagent_linux

FROM alpine as release

RUN apk update && apk upgrade && \
    apk add --no-cache bash git openssh curl

WORKDIR /

COPY --from=builder /ocagent_linux /ocagent_linux
#COPY --from=builder ./config.yaml config.yaml
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/

CMD ["/ocagent_linux"]
EXPOSE 55678 55679
