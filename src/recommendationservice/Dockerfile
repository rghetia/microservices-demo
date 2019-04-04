FROM python:2.7-slim
RUN apt-get update -qqy && \
	apt-get -qqy install wget && \
	rm -rf /var/lib/apt/lists/*
# show python logs as they occur
ENV PYTHONUNBUFFERED=0

# download the grpc health probe
RUN GRPC_HEALTH_PROBE_VERSION=v0.2.0 && \
    wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
    chmod +x /bin/grpc_health_probe

# get packages
WORKDIR /recommendationservice
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt

# add files into working directory
COPY . .

# TODO: remove this later
RUN apt-get update && apt-get install -y git
RUN git clone https://github.com/census-instrumentation/opencensus-python.git && cd opencensus-python && \
    git checkout 922702b6ac61615c8e3674f152c5ee6e25ccc96d && \
    pip install -e context/opencensus-context && \
    pip install -e . && \
    pip install -e contrib/opencensus-ext-ocagent && \
    pip install -e contrib/opencensus-ext-grpc

# set listen port
ENV PORT "8080"
EXPOSE 8080

ENTRYPOINT ["python", "/recommendationservice/recommendation_server.py"]
