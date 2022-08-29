# GitLab

Docker Compose project for [GitLab](https://docs.gitlab.com/ee/install/docker.html).

## Setup

```bash
cp .env.sample .env
```

- Domain configuration
    ```bash
    DOMAIN=example.com

    sed -i "s/\bexample.com\b/$DOMAIN/" .env
    ```
- Volume configuration and setup
    ```bash
    PREFIX=./data/

    CONF_PREFIX=${PREFIX}config/
    LOGS_PREFIX=${PREFIX}logs/
    DATA_PREFIX=${PREFIX}data/
    DB_PREFIX=${PREFIX}db/

    POSTRES_DIR=${DB_PREFIX}postgres
    RUNNER_CONF_DIR=${CONF_PREFIX}runner
    CONF_DIR=${CONF_PREFIX}gitlab
    LOGS_DIR=${LOGS_PREFIX}gitlab
    DATA_DIR=${DATA_PREFIX}gitlab

    sed -i -E "s/^(LOCAL_POSTRES_DIR)=.*/\1=$POSTRES_DIR/" .env
    sed -i -E "s/^(LOCAL_RUNNER_CONF_DIR)=.*/\1=$RUNNER_CONF_DIR/" .env
    sed -i -E "s/^(LOCAL_GITLAB_CONF_DIR)=.*/\1=$CONF_DIR/" .env
    sed -i -E "s/^(LOCAL_GITLAB_LOGS_DIR)=.*/\1=$LOGS_DIR/" .env
    sed -i -E "s/^(LOCAL_GITLAB_DATA_DIR)=.*/\1=$DATA_DIR/" .env

    sudo mkdir -p $POSTRES_DIR $RUNNER_CONF_DIR $CONF_DIR $LOGS_DIR $DATA_DIR
    ```
- Other configuration:
    - Please edit the `.env` file.
    - For more configuration options, see the `docker-compose.yml` file.
        - For configuring the variable `GITLAB_OMNIBUS_CONFIG`, see
            [GitLab » Docs » Configuring Omnibus GitLab](https://docs.gitlab.com/omnibus/settings/).

## First start

Note: To use `xsel` on a remote machine, you need to `ssh -X`.

```bash
sudo docker compose up -d

sudo docker compose logs -f

# Manually: Wait until the output seems regular. Ignore the gitlab-runner errors.

# Copy root password
sudo cat $CONF_DIR/initial_root_password | sed -En 's/Password: (.*)/\1/p' | tr -d '\n' | xsel -b

# TODO: https://$DOMAIN/admin/runners

sudo docker compose run gitlab-runner register
```

## Run

```bash
sudo docker compose up -d
```

## Stop

```bash
sudo docker compose down
```

## Maintenance

Running rake tasks: Example:

```bash
sudo docker compose exec --user git gitlab gitlab-rake gitlab:env:info
```

### Upgrading

See:
- https://docs.gitlab.com/ee/update/index.html#upgrade-paths
- https://docs.gitlab.com/ee/update/index.html#version-specific-upgrading-instructions

## References

- https://docs.gitlab.com/omnibus/settings/
