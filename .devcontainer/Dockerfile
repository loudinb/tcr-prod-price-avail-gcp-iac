# [Choice] Ubuntu version (use ubuntu-22.04 or ubuntu-18.04 on local arm64/Apple Silicon): ubuntu-22.04, ubuntu-20.04, ubuntu-18.04
ARG VARIANT=jammy
FROM ubuntu:${VARIANT}

ARG USERNAME="vscode"
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# [Optional] Uncomment this section to install additional OS packages.
RUN apt-get update && export DEBIAN_FRONTEND=noninteractive \
    && apt-get -y install --no-install-recommends vim wget curl git zsh \
    build-essential zlib1g-dev libncurses5-dev libgdbm-dev libnss3-dev \
    libssl-dev libreadline-dev libffi-dev libbz2-dev \
    ca-certificates sqlite3 libsqlite3-dev less

# ca certification update
RUN update-ca-certificates

# python
ADD "https://www.python.org/ftp/python/3.8.10/Python-3.8.10.tgz" /tmp/Python-3.8.10.tgz
RUN cd /tmp && tar -xf Python-3.8.10.tgz
RUN cd /tmp/Python-3.8.10 && ./configure --enable-optimizations
RUN cd /tmp/Python-3.8.10 && make -j 4 && make altinstall
RUN ln -s /usr/local/bin/python3.8 /usr/bin/python
RUN ln -s /usr/local/bin/pip3.8 /usr/bin/pip
RUN rm /tmp/Python-3.8.10.tgz && rm -rf /tmp/Python-3.8.10

# Python packages
# RUN pip install <package>

ADD "https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-394.0.0-linux-x86_64.tar.gz" /opt/gcloud-cli.tar.gz
RUN cd /opt && tar -xf gcloud-cli.tar.gz
RUN /opt/google-cloud-sdk/install.sh --quiet --usage-reporting=false --path-update=true
RUN rm /opt/gcloud-cli.tar.gz

# Create the user
RUN groupadd --gid ${USER_UID} ${USERNAME} \
    && useradd --uid ${USER_UID} --gid ${USER_GID} -s /usr/bin/zsh -m ${USERNAME} \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

USER vscode
ENV PATH="${PATH}:/opt/google-cloud-sdk/bin"

# Add Oh My Zsh
RUN sh -c "$(wget -O - https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
RUN sed -i 's/robbyrussell/eastwood/g' ~/.zshrc 

# [Optional] If your pip requirements rarely change, uncomment this section to add them to the image.
#COPY requirements.txt /tmp/pip-tmp/
#RUN pip3 --disable-pip-version-check --no-cache-dir install -r /tmp/pip-tmp/requirements.txt \
#   && rm -rf /tmp/pip-tmp



