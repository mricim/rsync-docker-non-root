FROM alpine

# Define arguments for customization
ARG USER=default
ARG UID=10002
ARG GID=10002
ARG MAX_CONNECTIONS=4
ENV HOME /home/$USER

# Install rsync and configure the environment
RUN apk --no-cache add rsync \
    && touch /var/run/rsyncd.lock \
    && chown $UID:$GID /var/run/rsyncd.lock \
    && chmod 644 /var/run/rsyncd.lock

# Create the user
RUN adduser -D -u $UID -g $GID -s /bin/bash $USER

# Create the persistence directory and set permissions
RUN mkdir /persistence \
    && chown -R $UID:$GID /persistence

# Copy configuration files into the container
COPY rsyncd.conf rsyncd.secrets /etc/

# Set permissions on configuration files
RUN chown $UID:$GID /etc/rsyncd.conf /etc/rsyncd.secrets \
    && chmod 600 /etc/rsyncd.secrets

# Configure the maximum number of connections in the configuration file
RUN sed -i "s/\$MAX_CONNECTIONS/$MAX_CONNECTIONS/g" /etc/rsyncd.conf

# Switch to the non-root user
USER $USER

# Set the working directory
WORKDIR $HOME

# Expose port 873
EXPOSE 873

# Default command when starting the container
CMD ["rsync", "--daemon", "--no-detach", "--config", "/etc/rsyncd.conf"]