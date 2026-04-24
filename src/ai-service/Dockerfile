# Use the official Python image as the base image
FROM python:3.12.7-alpine

# Set the working directory to /app
WORKDIR /app

# Set the build argument for the app version number
ARG APP_VERSION=0.1.0

# Runtime hardening settings
ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1

# Copy the requirements file into the container
COPY requirements.txt .

# Install the dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Create and use a non-root user
RUN addgroup -S app && adduser -S -G app app

# Copy only required application files
COPY --chown=app:app main.py ./
COPY --chown=app:app routers ./routers

# Expose port 5001 for the FastAPI application
EXPOSE 5001

# Set the environment variable for the app version number
ENV APP_VERSION=${APP_VERSION}

# Switch to least-privilege user
USER app

# Container health check (socket-level probe)
HEALTHCHECK --interval=30s --timeout=5s --start-period=20s --retries=3 \
  CMD python -c "import socket; s=socket.create_connection(('127.0.0.1', 5001), 2); s.close()" || exit 1

# Start the FastAPI application
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "5001"]