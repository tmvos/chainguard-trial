FROM cgr.dev/chainguard/python:latest-dev AS builder

ENV LANG=C.UTF-8
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1
ENV PATH="/app/venv/bin:$PATH"

WORKDIR /app

RUN python -m venv /app/venv
COPY requirements.txt .

RUN --mount=type=secret,id=chainguard_id,uid=65532,gid=65532 \
    --mount=type=secret,id=chainguard_token,uid=65532,gid=65532 \
    RAW_ID=$(cat /run/secrets/chainguard_id) && \
    # replace all forward slashes (/) with %2F
    CG_ID=${RAW_ID//\//%2F} && \
    CG_TOKEN=$(cat /run/secrets/chainguard_token) && \
    pip install --no-cache-dir \
    --index-url https://${CG_ID}:${CG_TOKEN}@libraries.cgr.dev/python/simple/ \
    -r requirements.txt

FROM cgr.dev/chainguard/python:latest

WORKDIR /app

COPY . /app

ENV PYTHONUNBUFFERED=1
ENV PATH="/venv/bin:$PATH"

ENV FLASK_APP=app.py
ENV FLASK_ENV=development
ENV PYTHONUNBUFFERED=1

COPY --from=builder /app/venv /venv

EXPOSE 8000

ENTRYPOINT ["python", "/app/app.py"]
