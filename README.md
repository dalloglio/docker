# Docker Development Environment

A comprehensive Docker-based development environment for PHP/Laravel applications with frontend development capabilities. This project provides a complete stack including PHP-FPM, Nginx, Redis, and a workspace container with Node.js support.

## 🏗️ Architecture

This Docker environment consists of the following services:

- **Workspace**: Development environment with PHP, Node.js, and Yarn
- **PHP-FPM**: PHP FastCGI Process Manager for handling PHP requests
- **Nginx**: Web server with SSL support and reverse proxy capabilities
- **Redis**: In-memory data structure store for caching and sessions

## 📁 Project Structure

```
docker/
├── docker-compose.yml              # Main orchestration file
├── workspace/                      # Development workspace container
│   └── Dockerfile                  # Workspace with PHP, Node.js, Yarn
├── php-fpm/                        # PHP-FPM service
│   ├── Dockerfile                  # PHP-FPM container
│   ├── laravel.ini                 # PHP configuration for Laravel
│   ├── opcache.ini                 # OPcache configuration
│   └── xlaravel.pool.conf          # PHP-FPM pool configuration
├── nginx/                          # Nginx web server
│   ├── Dockerfile                  # Nginx container
│   ├── nginx.conf                  # Main Nginx configuration
│   ├── startup.sh                  # SSL certificate generation script
│   └── sites/                      # Virtual host configurations
│       ├── default.conf            # Default site configuration
│       └── laravel.conf.example    # Laravel site example
├── redis/                          # Redis service
│   └── Dockerfile                  # Redis container
└── logs/                           # Application logs
    └── nginx/                      # Nginx access and error logs
```

## 🚀 Services Overview

### Workspace Container
- **Base Image**: `laradock/workspace:2.2-${PHP_VERSION}`
- **Features**:
  - PHP with configurable version
  - Node.js with NVM for version management
  - Yarn package manager
  - Non-root user (laradock) for file permissions
  - Timezone configuration
  - Development tools and utilities

### PHP-FPM Container
- **Base Image**: `laradock/php-fpm:2.2-${PHP_VERSION}`
- **Features**:
  - PHP-FPM with OPcache enabled
  - Optimized for Laravel applications
  - Configurable memory limits and execution timeouts
  - Dynamic process management
  - SSL certificate support

### Nginx Container
- **Base Image**: `nginx:alpine`
- **Features**:
  - Reverse proxy to PHP-FPM
  - SSL/TLS support with auto-generated certificates
  - Gzip compression
  - Virtual host configuration
  - Static file serving
  - WebSocket support for frontend development

### Redis Container
- **Base Image**: `redis:alpine`
- **Features**:
  - In-memory data storage
  - Persistent data volume
  - Configurable for caching and sessions

## ⚙️ Configuration

### Environment Variables

The following environment variables can be configured (typically in a `.env` file):

#### PHP Configuration
- `PHP_VERSION`: PHP version to use (e.g., 8.1, 8.2)
- `APP_CODE_PATH_HOST`: Host path to your application code
- `APP_CODE_PATH_CONTAINER`: Container path for application code

#### Workspace Configuration
- `WORKSPACE_NODE_VERSION`: Node.js version
- `WORKSPACE_YARN_VERSION`: Yarn version
- `WORKSPACE_PUID`: User ID for file permissions
- `WORKSPACE_PGID`: Group ID for file permissions
- `WORKSPACE_TIMEZONE`: Timezone setting

#### Nginx Configuration
- `NGINX_HOST_HTTP_PORT`: HTTP port mapping (default: 80)
- `NGINX_HOST_HTTPS_PORT`: HTTPS port mapping (default: 443)
- `NGINX_HOST_LOG_PATH`: Path for Nginx logs
- `NGINX_SITES_PATH`: Path for site configurations
- `NGINX_SSL_PATH`: Path for SSL certificates
- `NGINX_PHP_UPSTREAM_CONTAINER`: PHP-FPM container name
- `NGINX_PHP_UPSTREAM_PORT`: PHP-FPM port

#### Redis Configuration
- `REDIS_PORT`: Redis port mapping (default: 6379)
- `DATA_PATH_HOST`: Host path for Redis data persistence

#### Network Configuration
- `NETWORKS_DRIVER`: Docker network driver
- `VOLUMES_DRIVER`: Docker volume driver

### PHP Configuration Files

#### `php-fpm/laravel.ini`
- Memory limit: 512MB
- Upload max filesize: 20MB
- Post max size: 20MB
- Max execution time: 600 seconds
- Timezone: UTC

#### `php-fpm/opcache.ini`
- OPcache enabled
- Memory consumption: 256MB
- Max accelerated files: 30,000
- Timestamp validation enabled

#### `php-fpm/xlaravel.pool.conf`
- Dynamic process management
- Max children: 20
- Start servers: 2
- Min spare servers: 1
- Max spare servers: 3

## 🛠️ Usage

### Prerequisites
- Docker
- Docker Compose

### Quick Start

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd docker
   ```

2. **Create environment file**:
   ```bash
   cp .env.example .env
   # Edit .env with your configuration
   ```

3. **Start the services**:
   ```bash
   docker-compose up -d
   ```

4. **Access your application**:
   - Frontend: http://localhost (or configured port)
   - Laravel: http://laravel.test (after configuration)

### Laravel Setup

1. **Copy Laravel configuration**:
   ```bash
   cp nginx/sites/laravel.conf.example nginx/sites/laravel.conf
   ```

2. **Update the configuration** with your domain and paths

3. **Restart Nginx**:
   ```bash
   docker-compose restart nginx
   ```

### Development Commands

#### Access Workspace Container
```bash
docker-compose exec workspace bash
```

#### Run PHP Commands
```bash
docker-compose exec workspace php artisan migrate
docker-compose exec workspace composer install
```

#### Run Node.js Commands
```bash
docker-compose exec workspace npm install
docker-compose exec workspace yarn build
```

#### Access Redis
```bash
docker-compose exec redis redis-cli
```

### SSL Certificates

The Nginx container automatically generates self-signed SSL certificates on startup. For production use, replace the certificates in the SSL path with your own.

## 🔧 Customization

### Adding New Services

To add new services (e.g., MySQL, PostgreSQL):

1. Create a new directory with a Dockerfile
2. Add the service to `docker-compose.yml`
3. Configure networking and volumes as needed

### Custom PHP Extensions

To add PHP extensions, modify the `php-fpm/Dockerfile`:

```dockerfile
RUN docker-php-ext-install your-extension
```

### Custom Nginx Configuration

Add new site configurations in `nginx/sites/` and include them in the Nginx configuration.

## 📊 Monitoring and Logs

### View Logs
```bash
# All services
docker-compose logs

# Specific service
docker-compose logs nginx
docker-compose logs php-fpm
docker-compose logs redis
```

### Access Logs Directory
Logs are stored in the `logs/` directory and mounted to containers for easy access.

## 🔒 Security Considerations

- Non-root user in workspace container
- SSL/TLS encryption support
- Proper file permissions
- Isolated network configuration
- Configurable security settings

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test thoroughly
5. Submit a pull request

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 👨‍💻 Maintainer

**Ricardo Pires** - [ricardo.tech@live.com](mailto:ricardo.tech@live.com)

---

For issues, questions, or contributions, please open an issue or pull request on the repository.
