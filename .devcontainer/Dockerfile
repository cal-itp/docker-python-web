FROM cal-itp/docker-python-web:app

# install devcontainer requirements
COPY .devcontainer/requirements.txt .devcontainer/requirements.txt
RUN pip install -r .devcontainer/requirements.txt

COPY docs/requirements.txt docs/requirements.txt
RUN pip install -r docs/requirements.txt

# install pre-commit environments in throwaway Git repository
# https://stackoverflow.com/a/68758943
COPY .pre-commit-config.yaml .
RUN git init . && \
    pre-commit install-hooks && \
    rm -rf .git
