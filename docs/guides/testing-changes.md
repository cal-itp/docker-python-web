# Testing changes

Each time we update the underlying version of Python or the base OS the steps below are a good way to verify compatibility.

- Make sure the change is compatible with the python dependencies of dependent projects
- In your `docker-python-web` repository, rebuild the local image:

  ```bash
  docker compose build app --no-cache
  ```

## Test [Benefits](https://docs.calitp.org/benefits/) with the updated local base image

In your `benefits` repository:

1. In the benefits Dockerfile:

   ```bash
   ghcr.io/cal-itp/docker-python-web:main
   # becomes
   docker-python-web:app
   ```

1. Rebuild Benefits image

   ```bash
   docker compose build client --no-cache
   ```

1. Open Benefits devcontainer with "Rebuild Without Cache and Reopen in Container"
1. Run app locally and go through a few full flows
1. Verify that unit tests pass

## Test [eligibility-server](https://docs.calitp.org/eligibility-server/) with the updated local base image

In your `eligibility-server` repository

1. In the eligibility-server Dockerfile:

   ```bash
     ghcr.io/cal-itp/docker-python-web:main
     # becomes
     docker-python-web:app
   ```

1. Rebuild eligibility-server image

   ```bash
     docker compose build server --no-cache
   ```

1. Open eligibility-server devcontainer with "Rebuild Without Cache and Reopen in Container"
1. Verify that unit tests pass

And confirm the update works in benefits:

1. Use the local `eligibility_server:latest` image as the Benefits compose.yml `server` service
1. Rebuild the benefits container
1. Run app locally and test the Courtesy Card flow

## Follow-up

After a Python upgrade is complete, we follow-up in dependent projects above by updating:

- [`.github/workflows/.python-version`](https://github.com/cal-itp/benefits/blob/be4e7b37acfbd40dabd08ecf3dd2b5b227d8fd3d/.github/workflows/.python-version)
- `tool.black.target-version` in [`pyproject.toml`](https://github.com/cal-itp/benefits/blob/be4e7b37acfbd40dabd08ecf3dd2b5b227d8fd3d/pyproject.toml#L61)

We also update related projects that use an official image for the sake of parity.

- [littlepay](https://github.com/cal-itp/littlepay/pull/75)
- [eligibility-api](https://github.com/cal-itp/eligibility-api/pull/107)
