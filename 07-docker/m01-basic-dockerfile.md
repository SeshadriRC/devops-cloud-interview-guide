**Basic command**

```bash

FROM alpine

CMD ["echo", "welcome to docker"]

```


**Python basic program**

- Inside `app.py` write `print("welcome to docker")`

```bash
FROM python:3.12-slim

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]

```

**Distroless**

```bash
# Stage 1 - Builder stage
FROM python:3.12-slim AS builder

WORKDIR /app

COPY app.py .

# Stage 2 - Distroless runtime
FROM gcr.io/distroless/python3-debian12

WORKDIR /app

COPY --from=builder /app/app.py .

CMD ["/app/app.py"]

```
