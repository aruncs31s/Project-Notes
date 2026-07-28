# Initial Setting Up with Docker

```bash
docker run --rm \
  --network goexport_default \
  --env-file .env.docker \
  -p 8080:8080 \
  goexport:latest
```

