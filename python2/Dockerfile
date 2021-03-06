FROM alpine:3.7

# Install base packages and build dependencies
RUN set -ex; \
    apk add --no-cache --virtual python2-tools \
      python2 \
      py2-pip \
      py2-virtualenv; \
    apk add --no-cache --virtual command-line-tools \
      bash \
      busybox-extras \
      curl \
      jq \
      wget; \
    apk add --no-cache --virtual build-dependencies \
      g++ \
      gcc \
      git \
      libffi-dev \
      musl-dev \
      openssl-dev \
      python2-dev;


# Install BATS in /usr/local
RUN set -ex; \
    git clone https://github.com/sstephenson/bats.git; \
    cd bats; \
    ./install.sh /usr/local; \
    cd ../; \
    rm -rf bats;


# Install Python modules through pipenv
# set workdir to anything other than / so pipenv
# can properly install packages
WORKDIR /opt/pipenv
COPY python2/Pipfile Pipfile
COPY python2/Pipfile.lock Pipfile.lock
RUN set -ex; \
    pip2 install --no-cache-dir pipenv; \
    pipenv --python 2 install --system --verbose;\
    pip2 uninstall --yes pipenv; \
    rm -f Pipfile Pipfile.lock;


# Clean up unnecessary packages
RUN apk del build-dependencies;


# Prevent python from creating .pyc files and __pycache__ dirs
ENV PYTHONDONTWRITEBYTECODE=1


# Show stdout when running in docker compose (dont buffer)
ENV PYTHONUNBUFFERED=1


# Add a python startup file
COPY pystartup /usr/local/share/python/pystartup
ENV PYTHONSTARTUP=/usr/local/share/python/pystartup


# Add an entry point script.
COPY entry.sh /usr/local/bin/entry.sh
RUN set -ex; \
    chmod 755 /usr/local/bin/entry.sh;
