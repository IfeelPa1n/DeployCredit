# ------------------------
# Base Image
# ------------------------
FROM python:3.11-slim

# ------------------------
# System Dependencies (for OpenCV + OCR + general build)
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
# Set Working Directory
# ------------------------
WORKDIR /app

# ------------------------
# Copy Requirements
# ------------------------
COPY requirements.txt .

# ------------------------
# Upgrade pip and install dependencies
# ------------------------
RUN pip install --no-cache-dir --upgrade pip \
    && pip install --no-cache-dir "numpy<2" \
    && pip install --no-cache-dir -r requirements.txt \
    && pip install --no-cache-dir torch==2.2.2+cpu torchvision==0.17.2+cpu --extra-index-url https://download.pytorch.org/whl/cpu \
    && pip install --no-cache-dir gunicorn

# ------------------------
# Copy Project Files
# ------------------------
COPY . .

# ------------------------
# Set Environment Variables for Django
# ------------------------
ENV PYTHONUNBUFFERED=1
ENV DJANGO_SETTINGS_MODULE=AdminServer.settings

# ------------------------
# Collect Static Files
# ------------------------
RUN python manage.py collectstatic --noinput || true

# ------------------------
# Apply Migrations (optional, safe for build)
# ------------------------
RUN python manage.py migrate || true

# ------------------------
# Expose Port
# ------------------------
EXPOSE 8000

# ------------------------
# Run Gunicorn
# ------------------------
CMD ["gunicorn", "AdminServer.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "4"]
