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
   && apt-get install -y php8.3-cli php8.3-dev \
      php8.3-pgsql php8.3-gd php8.3-imagick \
      php8.3-curl \
      php8.3-imap php8.3-mysql php8.3-mbstring \
      php8.3-xml php8.3-zip php8.3-bcmath php8.3-soap \
      php8.3-intl php8.3-readline \
      php8.3-msgpack php8.3-igbinary php8.3-redis php8.3-swoole \
      php8.3-memcached php8.3-pcov php8.3-xdebug \
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

RUN setcap "cap_net_bind_service=+ep" /usr/bin/php8.3

RUN groupadd --force -g $WWWGROUP devcon
RUN useradd -ms /bin/bash --no-user-group -g $WWWGROUP -u 1337 devcon

# allow devContainer use sudo without password
RUN echo devcon ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/devcon \
   && chmod 0440 /etc/sudoers.d/devcon

COPY start-container /usr/local/bin/start-container
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY php.ini /etc/php/8.3/cli/conf.d/99-devcon.ini
RUN chmod +x /usr/local/bin/start-container

ENTRYPOINT ["start-container"]
