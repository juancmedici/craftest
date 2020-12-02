FROM python:3.7

RUN apt-get update \
    && apt-get install -y --no-install-recommends postgresql-client \
    && apt-get install -y libpq-dev python-dev \
    && apt-get install -y graphviz-dev \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir /code

WORKDIR /code
COPY requirements.txt .
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

EXPOSE 8000
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
