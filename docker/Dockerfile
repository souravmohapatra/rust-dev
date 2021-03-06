FROM ubuntu:20.04

ARG USER_ID=1000
ARG GROUP_ID=1000

ARG DEBIAN_FRONTEND=noninteractive

# Install dependencies
RUN apt-get update && \
    apt-get install -y build-essential cmake python3 libboost-filesystem-dev  \
    libboost-system-dev libboost-program-options-dev git ninja-build clang \
    gcc-arm-none-eabi wget golang \
    python3-distutils python3-tabulate python3-pip cm-super dvipng \
    vim neovim cscope ctags sudo zsh

RUN pip install matplotlib==3.5.1
RUN pip install pandas==1.4.1

# Create a new user
RUN addgroup --gid $GROUP_ID user
#RUN useradd -u $USER_ID -g $GROUP_ID -ms /bin/bash user

# Make the user able to do sudo without a password
RUN adduser --uid $USER_ID --gid $GROUP_ID --disabled-password --gecos '' user

#  Add new user docker to sudo group
RUN adduser user sudo

# Ensure sudo group users are not
# asked for a password when using
# sudo command by ammending sudoers file
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Remove the sudo messasge
RUN touch /home/user/.sudo_as_admin_successful

# Change the working directory
WORKDIR /home/user

# Change the user
USER user

# Install gllvm (https://github.com/SRI-CSL/gllvm)
RUN go get github.com/SRI-CSL/gllvm/cmd/...

## Configure the environment
RUN echo "export PATH=$HOME/go/bin:\$PATH" >> "$HOME/.env"
RUN echo "source $HOME/.env" >> "$HOME/.bashrc"

# Install dependencies
RUN sudo apt-get update && \
    sudo apt-get install -y software-properties-common

# Download my dotfiles
RUN git clone https://github.com/souravmohapatra/my-dotfiles.git

# Configure
RUN sed -i.bak -e '5,6d' ~/my-dotfiles/configurations/home/mohapatr/.bashrc
RUN cp ~/my-dotfiles/configurations/home/mohapatr/.bashrc ~/.bashrc
RUN sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
RUN zsh

# Install and configure rust
RUN sudo apt install curl
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
RUN source $HOME/.cargo/env
