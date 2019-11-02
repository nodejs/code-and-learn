FROM ubuntu:bionic

# Install tools
RUN apt-get update
RUN apt-get install -y build-essential python ninja-build vim git sudo

# Expose ports used by net tests
ENV NODE_COMMON_PORT=12346
EXPOSE 12346
# debug port used by v8 debugger
EXPOSE 5858
# debug port used by v8 insepctor
EXPOSE 9229

# Set up user node-dev: the tests fail when run by the root
RUN adduser --disabled-password --gecos '' node-dev
RUN adduser node-dev sudo
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers
USER node-dev
WORKDIR /home/node-dev
ENV HOME /home/node-dev

# Clone the code and build
RUN git clone --depth=1 https://github.com/nodejs/node.git
WORKDIR /home/node-dev/node
RUN python2 ./configure --ninja
ENV BUILD_WITH ninja
RUN make -j2 test
