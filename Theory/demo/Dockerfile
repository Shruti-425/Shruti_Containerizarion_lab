# Use lightweight Python image
FROM python

# Set working directory inside container
WORKDIR /home/app

# Install dependency
RUN pip install numpy

# Copy application code
COPY ./app.py .

# Run the program
CMD ["python", "app.py"]

# runtime mein dont copy the whole code image mein soource code missing