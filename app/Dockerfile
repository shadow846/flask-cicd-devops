# Use official Python image
FROM python:3.10-slim

# Set working directory
WORKDIR /app

# Copy dependency file
COPY requirements.txt .

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy source code
COPY . .

# Expose the port Flask runs on
EXPOSE 5000

# Start the Flask app
CMD ["python", "app.py"]
