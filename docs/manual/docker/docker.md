# Docker (Practical Notes)

## Images
```bash
docker pull nginx:latest
docker build -t myapp:dev .
docker images
```

## Containers
```bash
docker run --name web -p 8080:80 nginx:latest
docker ps
docker logs -f web
docker exec -it web sh
docker stop web && docker rm web
```

## Volumes
```bash
docker volume create mydata
docker run -v mydata:/var/lib/postgresql/data postgres
```

## Networks
```bash
docker network create appnet
docker run --network appnet --name api myapp:dev
```

## Cleanup
```bash
docker system prune
docker image prune
```

## Compose
See `docker-compose.md` for a minimal multi-service example.
