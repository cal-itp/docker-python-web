# Publish an image

Historically, a new image was published each time a commit was pushed to `main`. Now we use [tag-based deployment](https://github.com/cal-itp/docker-python-web/issues/73).

The steps to release are nearly identical to [Benefits](https://docs.calitp.org/benefits/guides/release/), with the exception that we use a [SemVer](https://semver.org/) numbering scheme.

## Decide on the new version number

Given a version number `MAJOR.MINOR.PATCH`, increment the:

- MAJOR version when you make incompatible API changes
- MINOR version when you add functionality in a backward compatible manner
- PATCH version when you make backward compatible bug fixes

Images are published to the GitHub Container Registry when a tag is pushed with a version number similar to either of the following:

```bash
# genuine release
1.2.3
# release candidate
2.3.4-rc.1
```
