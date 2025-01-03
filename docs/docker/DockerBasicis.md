To run a Docker container that saves its state on exit and then commit it to DockerHub, you can
follow these steps:

### Step-by-Step Guide

1. **Create a Dockerfile**: Ensure you have a `Dockerfile` that defines your container image.
Here’s a basic example:

    ```Dockerfile
    # Use an official base image
    FROM python:3.9-slim

    # Set the working directory
    WORKDIR /app

    # Copy dependencies file (if needed)
    COPY requirements.txt requirements.txt
    RUN pip install --no-cache-dir -r requirements.txt

    # Copy application code
    COPY . .

    # Define the command to run your application
    CMD ["python", "app.py"]
    ```

2. **Build Your Docker Image Locally**: Build the Docker image locally using the `Dockerfile`.

    ```sh
    docker build -t my-app-image:latest .
    ```

    Replace `my-app-image` with your desired image name and `latest` with a tag if needed (e.g.,
`v1.0`).

3. **Run the Docker Container**: Run the container in an interactive mode that ensures its state
is saved when it exits. You can use the `--name` flag to give your container a name and
`--rm=true` to automatically remove the container when it stops, unless you want to preserve it
temporarily for inspection.

    ```sh
    docker run --name my-running-app --rm=true -d my-app-image:latest
    ```

    - `--name my-running-app`: Names the container `my-running-app`.
    - `--rm=true`: Automatically removes the container when it exits unless specified otherwise.
    - `-d`: Runs the container in detached mode (in the background).

4. **Verify Container State**: Ensure the container exits gracefully or intentionally stop it to
capture its state.

    ```sh
    docker stop my-running-app
    ```

    Alternatively, if you stopped it manually or want to simulate an exit condition:

    ```sh
    docker exec my-running-app exit  # Force exit if needed
    ```

5. **Commit the Container to a New Image**: Use the `docker commit` command to create a new
image from the stopped container’s state.

    ```sh
    docker commit my-running-app my-app-image:committed
    ```

    - `my-running-app`: The name of the container you want to commit.
    - `my-app-image:committed`: The new tag for your committed image.

6. **Push the Image to DockerHub**: Log in to DockerHub and push the newly committed image.

    ```sh
    docker login
    docker push myusername/my-app-image:committed
    ```

    Replace `myusername` with your DockerHub username and `my-app-image:committed` with your
desired image tag.

### Example Workflow

Here’s a consolidated example workflow:

```sh
# Step 1: Build the Docker image locally
docker build -t myusername/my-app-image:latest .

# Step 2: Run the container
docker run --name my-running-app --rm=true -d myusername/my-app-image:latest

# Step 3: Stop or intentionally exit the container
docker stop my-running-app

# Step 4: Commit the container to a new image
docker commit my-running-app myusername/my-app-image:committed

# Step 5: Push the image to DockerHub
docker login
docker push myusername/my-app-image:committed
```

### Notes

- **Data Persistence**: If your application needs persistent storage, consider using Docker
volumes or bind mounts to ensure data persists across container restarts.
- **Image Tagging**: Use meaningful tags like version numbers or dates for better management and
tracking of your images.
- **Error Handling**: Always check for errors during each step and ensure your Docker
environment is correctly configured.

By following these steps, you can effectively manage and commit the state of your Docker
container for pushing to DockerHub.
