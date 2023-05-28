# Docker Images
## PHP
### Includes
  - php-mysql
  - php-pgsql
  - php-redis
  - php-gd
  - php-curl
  - php-mbstring
  - php-imagick
### Version
  - 7.4
  - 8.0
  - 8.1
  - 8.2

### How to build
    docker build --build-arg WWWGROUP={GROUPID} -t {NAME}:{TAG} ./php/{REPO_VERSION_PHP}