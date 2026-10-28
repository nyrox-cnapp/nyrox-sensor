# Nyrox runtime sensor: the per-node agent of the Nyrox sensor chart.
#
# Built on Falco (The Falco Authors, CNCF, Apache License 2.0). The base image
# already carries the container plugin and the default rules, and Nyrox's own
# rules are added here, so the sensor downloads nothing when it starts: the
# chart runs it with falcoctl turned off, which is what lets it install where
# there is no route to the internet.
ARG FALCO_VERSION=0.45.0
FROM falcosecurity/falco:${FALCO_VERSION}
ARG FALCO_VERSION

LABEL org.opencontainers.image.title="Nyrox runtime sensor" \
      org.opencontainers.image.vendor="NYVRA" \
      org.opencontainers.image.source="https://github.com/nyrox-cnapp/nyrox-sensor" \
      org.opencontainers.image.description="Runtime detection for Nyrox: watches system calls in containers with eBPF. Built on Falco ${FALCO_VERSION} (Apache-2.0)." \
      org.opencontainers.image.licenses="Apache-2.0"

COPY rules/ /etc/falco/rules.d/
COPY licenses/ /usr/share/nyrox/licenses/
COPY THIRD_PARTY_NOTICES.md /usr/share/nyrox/THIRD_PARTY_NOTICES.md
