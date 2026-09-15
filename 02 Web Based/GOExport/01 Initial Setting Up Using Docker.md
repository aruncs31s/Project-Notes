# Initial Setting Up with Docker

```bash
docker run --rm \
  --network goexport_default \
  --env-file .env.docker \
  -p 8080:8080 \
  goexport:latest
```


```bash
HTTP_ADDR=:8080
RABBITMQ_URL=amqp://guest:guest@rabbitmq:5672/
# RABBITMQ_URL=amqp://guest:guest@[::]:5672/
PDF_EXPORT_QUEUE=pdf.exports
S3_BUCKET=pdf-export
AWS_REGION=ap-south-1
# Optional for MinIO/S3-compatible storage
S3_ENDPOINT=http://minio:9000
AWS_ACCESS_KEY_ID=admin
AWS_SECRET_ACCESS_KEY=supersecretpassword

```
