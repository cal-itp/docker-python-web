# declare default build args for later stages
ARG PYTHON_VERSION=3.12 \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    USER=calitp \
    USER_UID=1000 \
    USER_GID=1000

FROM python:3.12

# renew top-level args in this stage
ARG PYTHON_VERSION \
    PYTHONDONTWRITEBYTECODE \
    PYTHONUNBUFFERED \
    USER \
    USER_UID \
    USER_GID

# set env vars for the user, including HOME
ENV PYTHONUNBUFFERED=${PYTHONUNBUFFERED} \
    PYTHONDONTWRITEBYTECODE=${PYTHONDONTWRITEBYTECODE} \
    HOME=/home/${USER} \
    USER=${USER} \
    PATH="/${USER}/.local/bin:$PATH" \
    # update env for local pip installs
    # see https://docs.python.org/3/using/cmdline.html#envvar-PYTHONUSERBASE
    # since all `pip install` commands are in the context of $USER
    # $PYTHONUSERBASE is the location used by default
    PYTHONUSERBASE="/${USER}/.local" \
    # where to store the pip cache
    # https://pip.pypa.io/en/stable/cli/pip/#cmdoption-cache-dir
    PIP_CACHE_DIR="/${USER}/.cache/pip" \
    GUNICORN_CONF="/$USER/run/gunicorn.conf.py"

EXPOSE 8000

USER root
# install apt packages using the archives and lists cache
RUN --mount=type=cache,id=apt-archives,sharing=locked,target=/var/cache/apt/archives \
    --mount=type=cache,id=apt-lists,sharing=locked,target=/var/lib/apt/lists \
    groupadd --gid ${USER_GID} ${USER} 2>/dev/null || true && \
    useradd --uid ${USER_UID} --gid ${USER_GID} --create-home --shell /bin/bash ${USER} && \
    # pip cache dir must be created and owned by the user to work with BuildKit cache
    mkdir -p ${PIP_CACHE_DIR} && \
    # own the parent directory of PIP_CACHE_DIR
    chown -R ${USER}:${USER} /${USER}/.cache && \
    # setup $USER permissions for nginx
    mkdir -p /var/cache/nginx && \
    chown -R $USER:$USER /var/cache/nginx && \
    mkdir -p /var/lib/nginx && \
    chown -R $USER:$USER /var/lib/nginx && \
    mkdir -p /var/log/nginx && \
    chown -R $USER:$USER /var/log/nginx && \
    touch /var/log/nginx/error.log && \
    chown $USER:$USER /var/log/nginx/error.log && \
    touch /var/run/nginx.pid && \
    chown -R $USER:$USER /var/run/nginx.pid && \
    # setup directories and permissions for gunicorn, (eventual) app
    mkdir -p /$USER/app && \
    mkdir -p /$USER/run && \
    chown -R $USER:$USER /$USER && \
    # install server components
    apt-get update && \
    apt-get install -y --no-install-recommends build-essential nginx gettext && \
    # this cleanup is still important for the final image layer size
    # remove lists from the image layer, but they remain in the BuildKit cache mount
    rm -rf /var/lib/apt/lists/*

# enter run (gunicorn) directory
WORKDIR /$USER/run

# copy gunicorn config file
COPY appcontainer/gunicorn.conf.py gunicorn.conf.py
# overwrite default nginx.conf
COPY appcontainer/nginx.conf /etc/nginx/nginx.conf

# switch to non-root $USER
USER $USER

# install python dependencies
COPY appcontainer/requirements.txt requirements.txt
RUN --mount=type=cache,id=pipcache,target=${PIP_CACHE_DIR},uid=${USER_UID},gid=${USER_GID} \
    python -m pip install --user --upgrade pip && \
    pip install --user -r requirements.txt

# enter app directory
WORKDIR /$USER/app

# basic bash entrypoint
ENTRYPOINT ["/bin/bash"]
