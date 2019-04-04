FROM python:3-slim as base

FROM base as builder

# get packages
COPY requirements.txt .
RUN pip install -r requirements.txt

FROM base as final

# Enable unbuffered logging
ENV PYTHONUNBUFFERED=1

RUN apt-get -qq update \
    && apt-get install -y --no-install-recommends \
        wget

# Download the grpc health probe
RUN GRPC_HEALTH_PROBE_VERSION=v0.2.0 && \
    wget -qO/bin/grpc_health_probe https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 && \
    chmod +x /bin/grpc_health_probe

WORKDIR /email_server

# Grab packages from builder
COPY --from=builder /usr/local/lib/python3.7/ /usr/local/lib/python3.7/

# Add the application
COPY . .

# TODO: remove this later
RUN apt-get update && apt-get install -y git
RUN git clone https://github.com/census-instrumentation/opencensus-python.git && cd opencensus-python && \
    git checkout 922702b6ac61615c8e3674f152c5ee6e25ccc96d && \
    pip install -e context/opencensus-context && \
    pip install -e . && \
    pip install -e contrib/opencensus-ext-ocagent && \
    pip install -e contrib/opencensus-ext-grpc

EXPOSE 8080
ENTRYPOINT [ "python", "email_server.py" ]
