#FROM lyft/envoy:4ef8079e9ff3056bab5fe9485e7fd2bc69d27dc5
FROM dwflynn/envoy-debug:latest

# This Dockerfile is set up to install all the application-specific stuff into
# /application.
#
# NOTE: If you don't know what you're doing, it's probably a mistake to 
# blindly hack up this file.

# We need curl, pip, and dnsutils (for nslookup).
RUN apt-get update && apt-get -q install -y \
    curl \
    python3-pip \
    dnsutils

# Set WORKDIR to /application which is the root of all our apps then COPY 
# only requirements.txt to avoid screwing up Docker caching and causing a
# full reinstall of all dependencies when dependencies are not changed.

WORKDIR /application
COPY requirements.txt .

# Install application dependencies
RUN pip3 install -r requirements.txt

# COPY the app code and configuration into place, then perform any final
# configuration steps.

COPY envoy-restarter.py .
COPY envoy-wrapper.sh .
RUN chmod 755 envoy-wrapper.sh
COPY ambassador.py envoy.py utils.py VERSION.py ./

COPY envoy-template.json .

# NOT A BUG! this is for bootstrapping.
COPY envoy-template.json /etc/envoy.json

# COPY the entrypoint script and make it runnable.
COPY entrypoint.sh .
RUN chmod 755 entrypoint.sh

ENTRYPOINT [ "./entrypoint.sh" ]
