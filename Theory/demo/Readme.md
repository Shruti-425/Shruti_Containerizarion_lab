# Test and debug
docker run --name testpyl -it -y ./:/app.python pip install numpy

# Image building
docker build -t pythonapp:01 .

# Run container from Image
docker run --rm --name testpy pythonapp:01