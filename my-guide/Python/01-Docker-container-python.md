## Python script to run a container using Docker SDK - User provides image name as input

[SDK-Link](https://docker-py.readthedocs.io/en/stable/)

### Short explanation
You can use the `docker` Python SDK to programmatically start a Docker container. This script takes an `image` name as input, pulls the image if it doesn’t exist locally, and runs a container based on it.

### Answer
Use `docker.from_env()` to connect to the local Docker engine and run the container with the provided image name.

### Detailed explanation (with examples)

Here is the complete Python script:

- Before executing below script install the docker SDK

```
pip3 install docker
```

```
import docker

# Initialize Docker client, it will connect to the running docker socket
client = docker.from_env()

# Get image name from user input
image_name = input("Enter the Docker image name (e.g., nginx:latest): ").strip()

try:
    # Pull the image (if not present locally)
    print(f"Pulling image '{image_name}'...")
    client.images.pull(image_name)
    print(f"Image '{image_name}' pulled successfully.")

    # Run the container
    print(f"Running container from image '{image_name}'...")
    container = client.containers.run(image_name, detach=True)
    print(f"Container started with ID: {container.id[:12]}")

except docker.errors.ImageNotFound:
    print(f"Error: Image '{image_name}' not found.")
except docker.errors.APIError as e:
    print(f"Docker API error: {e}")
except Exception as e:
    print(f"Unexpected error: {e}")
```

### Example usage

When you run the script:

    Enter the Docker image name (e.g., nginx:latest): alpine

Expected output:

    Pulling image 'alpine'...
    Image 'alpine' pulled successfully.
    Running container from image 'alpine'...
    Container started with ID: 3e1fabc2d789

This script assumes Docker is installed and the user has permission to run Docker commands. It runs the container in the background (`detach=True`) without any specific command or port mapping.
