FROM php:7.2-apache

# Install / update mandatory package
RUN apt-get update || apt-get upgrade
RUN apt-get install -y gnupg2 \
    libicu-dev \
    libfreetype6-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libxml2-dev \
    nano unzip wget curl


# git, be careful to update the git-completion.bash file if the git version changes
RUN apt-get install -y git bash-completion git-flow \
    && curl -sS https://raw.githubusercontent.com/git/git/v2.11.0/contrib/completion/git-completion.bash  > ~/.git-completion.bash \
    && curl -sS https://raw.githubusercontent.com/git/git/v2.11.0/contrib/completion/git-prompt.sh  > ~/.git-prompt.sh \
    && curl -sS https://raw.githubusercontent.com/petervanderdoes/git-flow-completion/master/git-flow-completion.bash  > ~/.git-flow-completion.bash \
    && echo "\nsource ~/.git-prompt.sh\nsource ~/.git-completion.bash\nsource ~/.git-flow-completion.bash\n" >> ~/.bashrc

# symfony-cli
RUN wget https://get.symfony.com/cli/installer -O - | bash && mv ~/.symfony/bin/symfony /usr/local/bin/symfony

# composer
RUN php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" \
    && php -r "if (hash_file('sha384', 'composer-setup.php') === '756890a4488ce9024fc62c56153228907f1545c228516cbf63f885e036d37e9a59d27d63f46af1d4d07ee0f76181c7d3') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" \
    && php composer-setup.php --install-dir=/usr/local/bin --filename=composer && chmod +x /usr/local/bin/composer && php -r "unlink('composer-setup.php');"

# nodejs
RUN curl -sL https://deb.nodesource.com/setup_14.x | bash - && apt-get install -y nodejs

# yarn
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
    && apt-get update && apt-get install yarn

# PHP server configuration
RUN mv "$PHP_INI_DIR/php.ini-development" "$PHP_INI_DIR/php.ini"
COPY custom.ini "$PHP_INI_DIR//conf.d/"
COPY opcache.ini "$PHP_INI_DIR//conf.d/"
COPY 000-default.conf "/etc/apache2/sites-enabled/"
RUN cp /etc/apache2/mods-available/rewrite.load /etc/apache2/mods-enabled/ && \
    cp /etc/apache2/mods-available/headers.load /etc/apache2/mods-enabled/

# configuration de gd
RUN docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/

# PHP extension
RUN pecl install apcu && docker-php-ext-enable apcu && docker-php-ext-install -j$(nproc) intl pdo_mysql gd zip bcmath soap opcache exif

# customization of the prompt, [Docker]user@localhost:path (git branch)
RUN echo "\n\nexport PS1='\[\e[38;5;39m\][Docker]\[\033[00m\]\u@\h:\w\[\033[0;33m\]\$(__git_ps1)\[\033[00m\] \\n\[\e[38;5;39m\]\\$\[\033[00m\] '" >> ~/.bashrc

