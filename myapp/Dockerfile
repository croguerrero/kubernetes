FROM python:3.8

COPY . /app
WORKDIR /app

RUN python -m pip install --upgrade pip
RUN pip install -r requirements.txt

CMD ["python", "-u", "server.py"]