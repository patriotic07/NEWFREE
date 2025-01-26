# Use the latest Python 3.13.1 slim image
FROM python:3.13.1-slim-buster

# Update system and install dependencies
RUN apt-get update -y && apt-get upgrade -y \
    && apt-get install -y --no-install-recommends \
        gcc \
        libffi-dev \
        musl-dev \
        ffmpeg \
        aria2 \
        python3-pip \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Set the working directory and copy the application files
COPY . /app/
WORKDIR /app/

# Install the required Python packages
RUN pip3 install --no-cache-dir -r requirements.txt
RUN pip3 install --no-cache-dir pytube yt-dlp selenium cloudscraper

# Set environment variable for cookies file
ENV COOKIES_FILE_PATH="/app/youtube_cookies.txt"

# Use gunicorn to serve the app with multiple workers for better performance
CMD gunicorn -w 4 -b 0.0.0.0:8000 app:app & python3 main.py
