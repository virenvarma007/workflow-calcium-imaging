FROM python:3.9-slim

RUN \
    adduser --system --disabled-password --shell /bin/bash vscode && \
    # install docker
    apt-get update && \
    apt-get install ca-certificates curl gnupg lsb-release -y && \
    mkdir -m 0755 -p /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/debian/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null && \
    apt-get update && \
    apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y && \
    usermod -aG docker vscode && \
    apt-get clean

RUN \
    # dev setup
    apt update && \
    apt-get install sudo git bash-completion graphviz default-mysql-client s3fs procps -y && \
    usermod -aG sudo vscode && \
    echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers && \
    pip install --no-cache-dir --upgrade black pip && \
    echo '. /etc/bash_completion' >> /home/vscode/.bashrc && \
    echo 'export PS1="\[\e[32;1m\]\u\[\e[m\]@\[\e[34;1m\]\H\[\e[m\]:\[\e[33;1m\]\w\[\e[m\]$ "' >> /home/vscode/.bashrc && \
    # dircolors -b >> /home/vscode/.bashrc && \  # somehow fix colors
    apt-get clean
COPY ./requirements.txt /tmp/
RUN \
    # workflow dependencies
    apt-get install gcc ffmpeg libsm6 libxext6 -y && \
    # pip install --no-cache-dir Pygments==2.14.0  && \
    # git clone https://github.com/datajoint/CaImAn.git  && \
    # pip install --no-cache-dir -r ./CaImAn/requirements.txt  && \
    # pip install --no-cache-dir ./CaImAn && \
    pip install --no-cache-dir suite2p && \
    pip install --no-cache-dir -r /tmp/requirements.txt && \
    # clean up
    # rm -rf /tmp/CaImAn && \
    rm /tmp/requirements.txt && \
    apt-get clean

ENV DJ_HOST fakeservices.datajoint.io
ENV DJ_USER root
ENV DJ_PASS simple

ENV IMAGING_ROOT_DATA_DIR /workspaces/workflow-calcium-imaging/example_data
ENV DATABASE_PREFIX neuro_

USER vscode
CMD bash -c "sudo rm /var/run/docker.pid; sudo dockerd"