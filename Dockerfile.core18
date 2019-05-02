FROM ubuntu:bionic as builder

# Grab dependencies
RUN apt update
RUN apt dist-upgrade --yes
RUN apt install --yes curl sudo jq squashfs-tools

# Grab the core snap from the stable channel and unpack it in the proper place
RUN curl -L $(curl -H 'X-Ubuntu-Series: 16' 'https://api.snapcraft.io/api/v1/snaps/details/core' | jq '.download_url' -r) --output core.snap
RUN mkdir -p /snap/core
RUN unsquashfs -d /snap/core/current core.snap

# Grab the core18 snap from the stable channel and unpack it in the proper place
RUN curl -L $(curl -H 'X-Ubuntu-Series: 16' 'https://api.snapcraft.io/api/v1/snaps/details/core18' | jq '.download_url' -r) --output core18.snap
RUN mkdir -p /snap/core18
RUN unsquashfs -d /snap/core18/current core18.snap

# Grab the snapcraft snap from the stable channel and unpack it in the proper place
RUN curl -L $(curl -H 'X-Ubuntu-Series: 16' 'https://api.snapcraft.io/api/v1/snaps/details/snapcraft?channel=stable' | jq '.download_url' -r) --output snapcraft.snap
RUN mkdir -p /snap/snapcraft
RUN unsquashfs -d /snap/snapcraft/current snapcraft.snap

# Multi-stage build, only need the snaps from the builder. Copy them one at a
# time so they can be cached.
FROM ubuntu:bionic
COPY --from=builder /snap/core /snap/core
COPY --from=builder /snap/core18 /snap/core18
COPY --from=builder /snap/snapcraft /snap/snapcraft

# Install the snapcraft runner
COPY snapcraft-wrapper /snap/bin/snapcraft

# Generate locale
RUN apt update && apt dist-upgrade --yes && apt install --yes sudo locales && locale-gen en_US.UTF-8

# Set the proper environment
ENV LANG en_US.UTF-8
ENV LANGUAGE en_US:en
ENV LC_ALL en_US.UTF-8
ENV PATH=/snap/bin:$PATH
