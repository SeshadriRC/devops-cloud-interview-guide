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

