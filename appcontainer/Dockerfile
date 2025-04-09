FROM python:3.12

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    USER=calitp

EXPOSE 8000

    # create non-root $USER and home directory
RUN useradd --create-home --shell /bin/bash $USER && \
    # setup $USER permissions for nginx
    mkdir -p /var/cache/nginx && \
    chown -R $USER /var/cache/nginx && \
    mkdir -p /var/lib/nginx && \
    chown -R $USER /var/lib/nginx && \
    mkdir -p /var/log/nginx && \
    chown -R $USER /var/log/nginx && \
    touch /var/log/nginx/error.log && \
    chown $USER /var/log/nginx/error.log && \
    touch /var/run/nginx.pid && \
    chown -R $USER /var/run/nginx.pid && \
    # setup directories and permissions for gunicorn, (eventual) app
    mkdir -p /$USER/app && \
    mkdir -p /$USER/run && \
    chown -R $USER /$USER && \
    # install server components
    apt-get update && \
    apt-get install -qq --no-install-recommends build-essential nginx gettext && \
    python -m pip install --upgrade pip

# enter run (gunicorn) directory
WORKDIR /$USER/run

# switch to non-root $USER
USER $USER

# update env for local pip installs
# see https://docs.python.org/3/using/cmdline.html#envvar-PYTHONUSERBASE
# since all `pip install` commands are in the context of $USER
# $PYTHONUSERBASE is the location used by default
ENV PATH="$PATH:/$USER/.local/bin" \
    PYTHONUSERBASE="/$USER/.local"

# install python dependencies
COPY appcontainer/requirements.txt requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# copy gunicorn config file
COPY appcontainer/gunicorn.conf.py gunicorn.conf.py
ENV GUNICORN_CONF "/$USER/run/gunicorn.conf.py"

# overwrite default nginx.conf
COPY appcontainer/nginx.conf /etc/nginx/nginx.conf

# enter app directory
WORKDIR /$USER/app

# basic bash entrypoint
ENTRYPOINT ["/bin/bash"]
