# NAME:     discourse/discourse_dev
# VERSION:  release
FROM discourse/base:release
ENV RAILS_ENV development

#LABEL maintainer="Sam Saffron \"https://twitter.com/samsaffron\""

# Remove the code added on base image
RUN rm -rf /var/www/*

# Give discourse user no-passwd sudo permissions (for bundle install)
ADD sudoers.discourse /etc/sudoers.d/discourse

# get redis going
ADD redis.template.yml /pups/redis.yml
RUN /pups/bin/pups /pups/redis.yml

RUN locale-gen en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8

# get postgres going
ADD postgres.template.yml /pups/postgres.yml
RUN LANG=en_US.UTF-8 /pups/bin/pups /pups/postgres.yml

# add dev databases
ADD postgres_dev.template.yml /pups/postgres_dev.yml
RUN /pups/bin/pups /pups/postgres_dev.yml

# move default postgres_data out of the way
RUN mv /shared/postgres_data /shared/postgres_data_orig

# re-instantiate data on boot if needed (this will allow it to persist across
# invocations when used with a mounted volume)
ADD ensure-database /etc/runit/1.d/ensure-database

RUN wget -q -O - https://dl-ssl.google.com/linux/linux_signing_key.pub | sudo apt-key add - &&\
    echo "deb [arch=amd64] http://dl.google.com/linux/chrome/deb/ stable main" >> /etc/apt/sources.list.d/google.list &&\
    apt update &&\
    apt install -y google-chrome-stable firefox-esr &&\
    npm install -g eslint babel-eslint

# Install & Configure MailHog (https://github.com/mailhog/MailHog)
RUN wget -qO /tmp/mailhog https://github.com/mailhog/MailHog/releases/download/v1.0.1/MailHog_linux_amd64\
    && echo "e2ed634ded49929f089b20045581955ed217672078fd86082dd7a6c67c5d09c7 /tmp/mailhog" | sha256sum -c -\
    && mv /tmp/mailhog /usr/local/bin/mailhog\
    && chmod +x /usr/local/bin/mailhog
