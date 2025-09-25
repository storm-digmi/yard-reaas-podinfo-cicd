# ---- Stage 0: prendo il Lambda Web Adapter
FROM public.ecr.aws/awsguru/aws-lambda-adapter:0.9.1 AS aws-lambda-adapter

# ---- Stage 1: podinfo + adapter
FROM ghcr.io/stefanprodan/podinfo:latest

# Copio l'extension del Lambda Web Adapter nel path giusto
COPY --from=aws-lambda-adapter /lambda-adapter /opt/extensions/lambda-adapter

# Config per l'adapter: porta e readiness
ENV PORT=9898
ENV AWS_LWA_PORT=9898
ENV AWS_LWA_READINESS_CHECK_PATH=/healthz
