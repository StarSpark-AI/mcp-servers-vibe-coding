# Ubuntu-based container for testing workshop setup instructions
FROM ubuntu:22.04

# Prevent interactive prompts during package installation
ENV DEBIAN_FRONTEND=noninteractive

# Install base dependencies
RUN apt-get update && apt-get install -y \
    curl \
    wget \
    git \
    sudo \
    vim \
    ca-certificates \
    gnupg \
    lsb-release \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Create a non-root user for testing
RUN useradd -m -s /bin/bash workshop && \
    echo "workshop ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers

# Switch to workshop user
USER workshop
WORKDIR /home/workshop

# Copy setup instructions into container
COPY --chown=workshop:workshop . /home/workshop/setup-instructions/

CMD ["/bin/bash"]
