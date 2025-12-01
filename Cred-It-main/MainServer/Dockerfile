# ------------------------
# Base Image
# ------------------------
FROM python:3.11-slim

# ------------------------
# System Dependencies (for OpenCV + OCR)
# ------------------------
RUN apt-get update && apt-get install -y \
    build-essential \
    libglib2.0-0 \
    libsm6 \
    libxrender1 \
    libxext6 \
    libgl1 \
    poppler-utils \
    tesseract-ocr \
    curl \
    && rm -rf /var/lib/apt/lists/*

# ------------------------
# Set Work Directory
# ------------------------
WORKDIR /app

# ------------------------
# Copy Requirements
# ------------------------
COPY MainServer/requirements.txt .

# ------------------------
# Install Python libs (Torch CPU)
# ------------------------
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir torch==2.2.2+cpu torchvision==0.17.2+cpu \
       --extra-index-url https://download.pytorch.org/whl/cpu \
    && pip install --no-cache-dir -r requirements.txt

# ------------------------
# Copy Project Files
# ------------------------
COPY MainServer/ .   # <-- copy everything from MainServer

# ------------------------
# Collect Static (optional)
# ------------------------
RUN python manage.py collectstatic --noinput || true

# ------------------------
# Expose Port
# ------------------------
EXPOSE 8000

# ------------------------
# Run Gunicorn
# ------------------------
CMD ["gunicorn", "AdminServer.wsgi:application", "--bind", "0.0.0.0:8000"]
