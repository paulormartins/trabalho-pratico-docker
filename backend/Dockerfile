FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY run.py .
COPY start-backend.sh .
COPY guess/ ./guess/
COPY repository/ ./repository/

RUN chmod +x start-backend.sh

EXPOSE 5000

CMD ["./start-backend.sh"]