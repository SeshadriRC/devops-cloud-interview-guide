Here’s a simple example using:

Multi-stage Docker build

Distroless image

Python program printing "welcome to docker"


Project Structure

.
├── Dockerfile
└── app.py

app.py

print("welcome to docker")

Dockerfile

# Stage 1 - Builder stage
FROM python:3.12-slim AS builder

WORKDIR /app

COPY app.py .

# Stage 2 - Distroless runtime
FROM gcr.io/distroless/python3-debian12

WORKDIR /app

COPY --from=builder /app/app.py .

CMD ["/app/app.py"]

Build Image

docker build -t python-distroless .

Run Container

docker run python-distroless

Output:

welcome to docker

What is happening here?

Multi-stage build

First stage (builder) prepares application files.

Second stage copies only required files.

Final image becomes smaller and cleaner.


Distroless image

gcr.io/distroless/python3-debian12 contains:

Python runtime

Minimal required libraries


It does not contain:

bash

sh

package managers

unnecessary OS utilities


Benefits:

Smaller image

Better security

Reduced attack surface


One important point: You usually cannot do:

docker exec -it container sh

inside distroless containers because shell is absent.

---

Think of multi-stage build as creating 2 temporary containers during image build.

You are not running both. Docker builds Stage 1, then Stage 2.


---

Stage 1 — Builder stage

FROM python:3.12-slim AS builder

Start from a Python image and give this stage a name: builder

Imagine a temporary environment is created:

builder container
└── Python installed


---

WORKDIR /app

Creates/uses /app folder.

Current location becomes:

/app


---

COPY app.py .

Copies your local file into the builder stage.

Local machine:

project/
 ├── Dockerfile
 └── app.py

Inside builder stage:

/app
 └── app.py

At this point Stage 1 contains:

builder stage
└── /app
    └── app.py


---

Now Docker starts Stage 2.

Stage 2 — Final runtime image

FROM gcr.io/distroless/python3-debian12

Docker starts fresh again from a new image.

Important: Stage 2 does NOT automatically inherit Stage 1 files.

Currently Stage 2 looks like:

distroless image
(no app.py)


---

WORKDIR /app

Create/use /app.

/app


---

The important line

COPY --from=builder /app/app.py .

This means:

> "Copy a file from the earlier stage named builder."



Syntax:

COPY --from=<stage-name> <source> <destination>

Here:

FROM builder stage:
    /app/app.py

COPY TO current stage:
    .

. means current directory (/app).

So:

builder stage:
/app/app.py
        ↓
distroless stage:
/app/app.py


---

Finally:

CMD ["/app/app.py"]

When container starts:

Python runtime runs /app/app.py

Output:

welcome to docker


---

Visual flow:

LOCAL FILES
└── app.py

        ↓ COPY

STAGE 1 (builder)
python:3.12-slim
└── /app/app.py

        ↓ COPY --from=builder

STAGE 2 (final image)
distroless python
└── /app/app.py

        ↓ run

OUTPUT:
welcome to docker

Why do this?

Without multi-stage:

Final image contains everything from python:3.12-slim.

With multi-stage + distroless:

Final image contains only:

Python runtime

Your app file


No extra build tools, shell, apt, etc.

Smaller + more secure image.

---

## Ok in distroless cmd , no need to mention python command

Yes — in this specific distroless image, you do not need to mention python.

Because:

FROM gcr.io/distroless/python3-debian12

already has Python configured as the default entrypoint internally.

So this works:

CMD ["/app/app.py"]

Internally it behaves similar to:

python /app/app.py


---

If you use a normal Linux image like Alpine or Ubuntu, then you must specify Python explicitly:

FROM python:3.12-slim

CMD ["python", "/app/app.py"]

because those images do not automatically execute Python scripts.


---

Distroless Python images are specially designed so that:

Python runtime is preconfigured

Your script can be passed directly


That is why:

CMD ["/app/app.py"]

is enough.

---

# So first entrypoint will execute then cmd right

Yes. In Docker:

1. ENTRYPOINT runs first


2. CMD provides default arguments to ENTRYPOINT



Think like this:

ENTRYPOINT + CMD

combine together.


---

Example:

ENTRYPOINT ["python"]
CMD ["app.py"]

Docker actually runs:

python app.py


---

In distroless Python image, internally something similar already exists:

ENTRYPOINT ["python3"]

So when you write:

CMD ["/app/app.py"]

Docker combines them:

python3 /app/app.py


---

Another example:

ENTRYPOINT ["echo"]
CMD ["hello"]

Container execution becomes:

echo hello

Output:

hello


---

Important difference:

ENTRYPOINT

Main executable

Harder to override


CMD

Default arguments/default command

Easily overridden


Example:

docker run myimage hi

If Dockerfile has:

ENTRYPOINT ["echo"]
CMD ["hello"]

Result becomes:

echo hi

because runtime argument overrides CMD.

---