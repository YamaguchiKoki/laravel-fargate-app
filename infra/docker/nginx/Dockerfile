# Node.js stage to copy node binaries
FROM node:latest as node
WORKDIR /node
# node, npm, and yarn commands are copied from node image
COPY --from=node /usr/local/bin /usr/local/bin
COPY --from=node /usr/local/lib /usr/local/lib
COPY --from=node /opt /opt

# Nginx stage starts here
FROM nginx:1.20-alpine
LABEL maintainer="ucan-lab <yes@u-can.pro>"
SHELL ["/bin/ash", "-oeux", "pipefail", "-c"]

# Set timezone
ENV TZ=UTC

# Install system dependencies
RUN apk update && \
    apk add --no-cache --virtual .build-dependencies \
    g++ \
    bash

# Setup directories and permissions
WORKDIR /work/backend

# Assuming that you need node and npm commands to run build scripts for static files
# Copy node and npm binaries from node stage
COPY --from=node /usr/local/bin /usr/local/bin
COPY --from=node /usr/local/lib /usr/local/lib
COPY --from=node /opt /opt

# Adding a non-root user and switch to it
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Copy application files and set ownership to non-root user
COPY --chown=appuser:appgroup ./backend/public /work/backend/public

# Switch back to root to configure Nginx
USER root
COPY ./infra/docker/nginx/*.conf /etc/nginx/conf.d/

# Start Nginx server
CMD ["nginx", "-g", "daemon off;"]
