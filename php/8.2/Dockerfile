FROM ubuntu:22.04

LABEL maintainer="Dwi Alim Nurrohman"

ARG WWWGROUP

WORKDIR /var/www/html

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Jakarta

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update \
   && apt-get install -y gnupg gosu curl ca-certificates zip unzip git supervisor sqlite3 libcap2-bin libpng-dev python2 dnsutils librsvg2-bin \
   && curl -sS 'https://keyserver.ubuntu.com/pks/lookup?op=get&search=0x14aa40ec0831756756d7f66c4f4ea0aae5267a6c' | gpg --dearmor | tee /etc/apt/keyrings/ppa_ondrej_php.gpg > /dev/null \
   && echo "deb [signed-by=/etc/apt/keyrings/ppa_ondrej_php.gpg] https://ppa.launchpadcontent.net/ondrej/php/ubuntu jammy main" > /etc/apt/sources.list.d/ppa_ondrej_php.list \
   && apt-get update \
   && apt-get install -y php8.2-cli php8.2-dev \
      php8.2-pgsql php8.2-gd php8.2-imagick \
      php8.2-curl \
      php8.2-imap php8.2-mysql php8.2-mbstring \
      php8.2-xml php8.2-zip php8.2-bcmath php8.2-soap \
      php8.2-intl php8.2-readline \
      php8.2-msgpack php8.2-igbinary php8.2-redis php8.2-swoole \
      php8.2-memcached php8.2-pcov php8.2-xdebug \
   && php -r "readfile('https://getcomposer.org/installer');" | php -- --install-dir=/usr/bin/ --filename=composer \
   && apt-get -y autoremove \
   && apt-get clean \
   && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# My customizations
RUN apt-get update \
   && apt-get install -y sudo nano git curl \
   && curl -fsSL https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo gpg --dearmor -o /usr/share/keyrings/githubcli-archive-keyring.gpg \
   && chmod go+r /usr/share/keyrings/githubcli-archive-keyring.gpg \
   && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
   && apt update \
   && apt install gh -y

RUN setcap "cap_net_bind_service=+ep" /usr/bin/php8.2

RUN groupadd --force -g $WWWGROUP devcon
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 devcon

# allow devContainer use sudo without password
RUN echo devcon ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/devcon \
   && chmod 0440 /etc/sudoers.d/devcon

COPY start-container /usr/local/bin/start-container
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY php.ini /etc/php/8.2/cli/conf.d/99-devcon.ini
RUN chmod +x /usr/local/bin/start-container

ENTRYPOINT ["start-container"]
