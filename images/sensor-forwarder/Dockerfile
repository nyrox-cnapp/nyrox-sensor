# Nyrox sensor forwarder: receives the runtime sensor's alerts on the cluster
# and posts them to Nyrox's ingest address with the connector's token.
#
# Built on Falcosidekick (The Falco Authors, Apache License 2.0), unmodified.
ARG FALCOSIDEKICK_VERSION=2.32.0
FROM falcosecurity/falcosidekick:${FALCOSIDEKICK_VERSION}
ARG FALCOSIDEKICK_VERSION

LABEL org.opencontainers.image.title="Nyrox sensor forwarder" \
      org.opencontainers.image.vendor="NYVRA" \
      org.opencontainers.image.source="https://github.com/nyrox-cnapp/nyrox-sensor" \
      org.opencontainers.image.description="Forwards Nyrox runtime sensor alerts to Nyrox. Built on Falcosidekick ${FALCOSIDEKICK_VERSION} (Apache-2.0)." \
      org.opencontainers.image.licenses="Apache-2.0"

COPY licenses/ /usr/share/nyrox/licenses/
COPY THIRD_PARTY_NOTICES.md /usr/share/nyrox/THIRD_PARTY_NOTICES.md
