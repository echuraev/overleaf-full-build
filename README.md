# Self-hosted Overleaf

[![GitHub license](https://img.shields.io/github/license/echuraev/overleaf-full-build)](https://github.com/echuraev/overleaf-full-build/blob/main/LICENSE)
[![Docker Pulls](https://img.shields.io/docker/pulls/echuraev/overleaf-full)](https://hub.docker.com/r/echuraev/overleaf-full)

Build self-hosted overleaf with tex-full and track changes feature.

By default track changes feature is not available by default at
overleaf-community-edition (Issues [#1036](https://github.com/overleaf/overleaf/issues/1036#issuecomment-1446518123)
and [#1193](https://github.com/overleaf/overleaf/issues/1193)). To enable this
it is necessary to modify overleaf source code manually.

Moreover sometimes image on the dockerhub may not constrain the target
architecture, in my case it was impossible to use docker image for arm64
architecture. In this case it also might be necessary to build the docker image
locally.

## DockerHub

ARM64 image can be downloaded from the DockerHub:
```
docker pull echuraev/overleaf-full:arm64
```

## Build steps

1. `git clone git@github.com:overleaf/overleaf.git`
2. `cd overleaf`
3. Apply changes from [0001-Enable-track-changes-and-comments-feature.patch](0001-Enable-track-changes-and-comments-feature.patch)
    which were originally taken from here: https://github.com/yu-i-i/overleaf-cep/commit/96697182f4db4b9e6218daaef10b2b18e2735829
    ```
    git am 0001-Enable-track-changes-and-comments-feature.patch
    ```
3. `cd server-ce`
4. Build base image: `make build-base`
5. Build overleaf community image: `make build-community`
6. Change directory to the folder with this repo.
7. `docker build -t echuraev/overleaf-full:arm64 .`

    The original docker file was taken from here: https://github.com/tuetenk0pp/sharelatex-full/

    This will build docker image with full texlive support.

## Docker compose for self-hosting
If you want to deploy the Overleaf instance on your own server. You can use [docker-compose.yml](docker-compose.yml) file from this repository.
In this file you should modify the following parameters and variables:
- `volumes` - Change the local paths of all volumes to your local directory. In the file `/docker/overleaf` is used.
- `OVERLEAF_ADMIN_EMAIL` - Admin email address.
- `OVERLEAF_SITE_URL` - The full url to your Overleaf instance on the server.
- `OVERLEAF_EMAIL_FROM_ADDRESS` - Email address which will be used to send messages to users from Overleaf.
- `OVERLEAF_EMAIL_SMTP_USER` - Can be used the same address which was specified in `OVERLEAF_EMAIL_FROM_ADDRESS`.
- `OVERLEAF_EMAIL_SMTP_PASS` - Password to the `OVERLEAF_EMAIL_SMTP_USER`.
- If you are not using Google's smtp then you should also modify `OVERLEAF_EMAIL_SMTP_HOST` and `OVERLEAF_EMAIL_SMTP_PORT` variables.

## Acknowledgments
- [@tuetenk0pp](https://github.com/tuetenk0pp/) thanks for the Dockerfile which includes full TEX Live
    installation.
- [@yu-i-i](https://github.com/yu-i-i/) thanks for the branch with enabling changes tracking in the
    overleaf-community-edition.
