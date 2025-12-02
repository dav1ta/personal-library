```yaml
version: '3.8' # Specify the Docker Compose file format version

services:
  webapp:
    image: nginx:latest | custom-image:tag # Docker image to use
    build: # Options for building the image
      context: ./webapp | ./alternative-path # Build context
      dockerfile: Dockerfile | CustomDockerfile # Dockerfile to use
    container_name: my-custom-webapp | another-name # Custom name for the container
    command: ["nginx", "-g", "daemon off;"] | ["custom", "command"] # Command to run in the container
    entrypoint: ["/entrypoint.sh"] | ["/alternative.sh"] # Entrypoint for the container
    ports: # Ports to expose
      - "8080:80" # Map host port 8080 to container port 80
      - "8443:443" # Map host port 8443 to container port 443
    expose: # Expose ports without publishing them to the host machine
      - "8081" # Expose port 8081
    volumes: # Mount volumes
      - type: bind | volume
        source: ./app | named-volume
        target: /app | /alternative-path
    environment: # Environment variables
      - ENV_VAR=example | another_variable=value
    env_file: # Environment file
      - .env | another.env
    networks: # Networks to connect to
      mynetwork | another-network:
        aliases: # Network aliases
          - webapp-alias | alternative-alias
    depends_on: # Specify dependencies
      database | another-service:
        condition: service_started | service_healthy
    stop_grace_period: 30s | 1m # Grace period before stopping the container
    restart: on-failure | always | no # Restart policy
    labels: # Labels for the container
      com.example.label: example | another.label:value
    logging: # Logging configuration
      driver: "json-file" | "syslog" | "fluentd" # Logging driver
      options:
        max-size: "10m" | "5m"
        max-file: "3" | "5"
    tmpfs: # Temporary filesystems
      - /tmp | /another-tmp
    devices: # Devices to add to the container
      - "/dev/sda:/dev/sda" | "/dev/sdb:/dev/sdb"
    ulimits: # Ulimit options
      nproc: 65535 | 10000
      nofile:
        soft: 4096 | 1024
        hard: 8192 | 2048
    cap_add: # Capabilities to add
      - NET_ADMIN | AUDIT_CONTROL
    cap_drop: # Capabilities to drop
      - SYS_ADMIN | NET_RAW
    security_opt: # Security options
      - seccomp=unconfined | no-new-privileges
    network_mode: bridge | host | none # Network mode
    pid: "host" | "container:name" # PID namespace to use
    cpu_shares: 256 | 512 # CPU shares (relative weight)
    cpu_quota: 50000 | 100000 # CPU CFS quota
    mem_limit: "256m" | "512m" # Memory limit
    mem_reservation: "128m" | "256m" # Memory soft limit
    tty: true | false # Allocate a pseudo-TTY
    privileged: true | false # Extended privileges
    init: true | false # Use an init process
    cgroup_parent: my-cgroup | another-cgroup # Parent cgroup
    shm_size: "64m" | "128m" # Size of /dev/shm
    stop_signal: SIGTERM | SIGKILL # Signal to stop the container
    sysctls: # Kernel parameters
      - net.core.somaxconn=1024 | net.ipv4.tcp_tw_reuse=1
      - net.ipv4.tcp_syncookies=0 | net.ipv6.conf.all.disable_ipv6=1
    isolation: default | process | hyperv # Container isolation level
    dns: # Custom DNS servers
      - 8.8.8.8 | 1.1.1.1
      - 8.8.4.4 | 9.9.9.9
    dns_search: # DNS search domains
      - example.com | another-domain.com
    healthcheck: # Healthcheck configuration
      test: ["CMD", "curl", "-f", "http://localhost"] | ["CMD-SHELL", "echo 'healthcheck'"]
      interval: 10s | 1m # Interval for running the healthcheck
      timeout: 5s | 10s # Timeout for the healthcheck
      retries: 3 | 5 # Number of retries for the healthcheck
    extra_hosts: # Additional hosts
      - "otherhost:192.168.1.100" | "anotherhost:192.168.1.101"
    hostname: my-custom-hostname | alternative-hostname # Hostname of the container
    domainname: example.com | another-domain.com # Domain name of the container
    working_dir: /app | /another-directory # Working directory inside the container
    read_only: true | false # Mount the container's root filesystem as read only
    user: "1000:1000" | "2000:2000" # UID:GID to use when running the image
    secrets: # Secrets to expose to the service
      - my-secret | another-secret
    configs: # Configs to expose to the service
      - my-config | another-config
    networks:
    mynetwork | another-network:
    driver: bridge | overlay # Network driver
    ipam: # IP Address Management
    driver: default | custom-driver
    config:
    - subnet: "172.16.238.0/24" | "10.0.0.0/16"
    external: true | false # Use an external network

    volumes:
      my_volume | another_volume:
        driver: local | custom-driver  # Volume driver
        driver_opts:
          type: none | btrfs
          o: bind | nfs
          device: /path/to/my/data | /another/path

    secrets:
      my-secret | another-secret:
        file: ./secrets/my-secret.txt | ./another-secret.txt  # File to use for the secret
        external: false | true  # Whether the secret is external

    configs:
      my-config | another-config:
        file: ./configs/my-config.txt | ./another-config.txt  # File to use for the config
        external: true | false  # Whether the config is external

```
