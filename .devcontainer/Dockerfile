# this image is built and updated weekly
# https://github.com/cse-labs/codespaces-images

FROM ghcr.io/cse-labs/k3d:latest

#
RUN [ "curl", "-L", "https://github.com/fluxcd/flux2/releases/download/v0.28.4/flux_0.28.4_linux_amd64.tar.gz", "-s", "--output", "/tmp/flux.tar.gz" ]
RUN [ "tar", "xzf", "/tmp/flux.tar.gz" ]
RUN [ "mv", "flux", "/usr/local/bin/flux" ]
RUN [ "chmod", "0755", "/usr/local/bin/flux" ]

# some images require specific values
ARG USERNAME=vscode

# log the docker build start / complete events
RUN echo "$(date)    docker build start" >> /home/$USERNAME/status && \
    echo "$(date)    docker build complete" >> /home/$USERNAME/status && \
    chown -R $USERNAME:$USERNAME /home/$USERNAME
