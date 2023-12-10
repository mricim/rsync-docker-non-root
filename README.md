


# Rsync Server non-root Docker Configuration

This repository offers a Dockerized configuration to run an rsync server, **utilizing a non-root user**. 


## Repository Contents

- `docker-compose.yaml`: Defines the rsync-server service and its configurations.
- `files/`: Contains the Dockerfile and other necessary files for building the image.
- `rsyncd.conf`: Configuration file for the rsync server.
- `rsyncd.secrets`: Password file for user authentication.


## Usage

### Configuration

1. **docker-compose.yaml**: Adjust environment variables according to your needs.
    - `UID`, `GID`: User and group identifiers for the container.
    - `user:`: replace according `UID`, `GID`
    - `MAX_CONNECTIONS`: Maximum number of simultaneous connections allowed.
    - In `volumes:`: configure the directories where you want your data persistences to be saved.
Example: `- /data:/persistence/folder` in this example, **"data"** will be the folder where your persistence will be saved. In **"folder"** it will be the docker mount point

2. **files/rsyncd.conf**: Customize the rsync server configuration.
    - You can edit server configurations.
    - Add, edit or remove volumes (there is a sample one commented)
3. **files/rsyncd.secrets**: Create this file for user authentication.
    - Add the users and passwords. You must add one per line following the following format:
   ```yaml
   user:password
   user2:password2
   ``` 

### Build the Container:

   ```bash
   docker-compose build
   ```

### Start the Service:

   ```bash
   docker-compose up -d
   chown -R 10002:10002 /data 
   ```
   use `chown` on (host) directories that rsync should use

### **Client Usage:**

   ```bash
   rsync -avz /local/path rsync://user@localhost/data/
   ```
    
The `-z` option is for compression.

The `user` should be the user you created in `files/rsyncd.secrets`


## NOTE
Remember to use `chown -R 10002:10002 /folder_in_host` after starting the service (`up -d`). If you don't, and the container does not use root when starting rsync, it will not work.

If you decide to use root as the main user, use `daemon chroot` and `use chroot`, to lock rsync users within their folders.