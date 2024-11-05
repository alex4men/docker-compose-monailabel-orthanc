FROM projectmonai/monailabel:latest

RUN monailabel apps --download --name monaibundle --output /app

COPY datastore.py /usr/local/lib/python3.10/dist-packages/monailabel/endpoints/datastore.py
