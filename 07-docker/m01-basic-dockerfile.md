**Basic command**

```bash

FROM alpine

CMD ["echo", "welcome to docker"]

```


**Python basic program**

```bash
FROM python:3.12-slim

WORKDIR /app

COPY app.py .

CMD ["python", "app.py"]

```

