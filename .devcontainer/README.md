# Remote Containers pull auth bug repro

To reproduce this bug, you need a Docker Hub account with a private repository you can push to (or some other private image registry).

1. In `.devcontainer/devcontainer.json`, update `YOUR_DOCKERHUB` to refer to your own account or private registry. Commit and push.
2. In `.devcontainer/devcontainer.json`, uncomment lines 6-10
3. In Docker Hub or other registry, create a private repository called `devcontainer-pull-test`
4. From this directory, run `devcontainer build --workspace-folder . --image-name YOUR_DOCKERHUB/devcontainer-pull-test --push --buildkit` from the repository (again replacing `YOUR_DOCKERHUB`)
5. In VS Code, run `Remote-Containers: Clone Repository in Named Container Volume` and clone this repository. The repository fails to clone with output that looks something like this:

```
[21 ms] Remote-Containers 0.251.0 in VS Code 1.71.0 (784b0177c56c607789f9638da7b6bf3230d47a8c).
[20 ms] Start: Resolving Remote
[26 ms] Start: Check Docker is running
[26 ms] Start: Run: docker version --format {{.Server.APIVersion}}
[100 ms] Server API version: 1.41
[101 ms] Start: Run: docker volume ls -q
[155 ms] Start: Run: docker volume create --label vsch.local.repository=https://github.com/wandb/devcontainer-test.git/tree/master --label vsch.local.repository.unique=false --label vsch.local.repository.branch=master dc-test3
[210 ms] Start: Run: docker build -f /var/folders/wk/pbzbnm1j55nb32_8v0dvyr3w0000gn/T/vsch/bootstrap-image/0.251.0/bootstrap.Dockerfile -t vsc-volume-bootstrap /var/folders/wk/pbzbnm1j55nb32_8v0dvyr3w0000gn/T/vsch/bootstrap-image/0.251.0
[+] Building 1.5s (9/9) FINISHED
 => [internal] load build definition from bootstrap.Dockerfile             0.0s
 => => transferring dockerfile: 47B                                        0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for mcr.microsoft.com/vscode/devcontainers/b  1.4s
 => [1/4] FROM mcr.microsoft.com/vscode/devcontainers/base:0-alpine-3.14@  0.0s
 => [internal] load build context                                          0.0s
 => => transferring context: 5.90kB                                        0.0s
 => CACHED [2/4] RUN apk add --no-cache  nodejs  npm  make  g++  docker-c  0.0s
 => CACHED [3/4] RUN cd && npm i node-pty                                  0.0s
 => CACHED [4/4] COPY .vscode-remote-containers /root/.vscode-remote-cont  0.0s
 => exporting to image                                                     0.0s
 => => exporting layers                                                    0.0s
 => => writing image sha256:f003838d314628308758a3d7cd75b5bd4bd56f66131d1  0.0s
 => => naming to docker.io/library/vsc-volume-bootstrap                    0.0s

Use 'docker scan' to run Snyk tests against images to find vulnerabilities and learn how to fix them
[2008 ms] Cloning Github repository: wandb/devcontainer-test into /workspaces/devcontainer-test

[2008 ms] Start: Run: docker run -d --mount type=volume,src=dc-test3,dst=/workspaces -v /var/run/docker.sock:/var/run/docker.sock vsc-volume-bootstrap sleep infinity
[2237 ms] Start: Run in container: /bin/sh
[2239 ms] Start: Launching Remote-Containers helper.
[2239 ms] ssh-agent: SSH_AUTH_SOCK in container (/tmp/vscode-ssh-auth-d2c6586a65eeb453d225a77477381fa444f9822c.sock) forwarded to local host (/private/tmp/com.apple.launchd.4Cx3DcdyvD/Listeners).
[2239 ms] Start: Run: gpgconf --list-dir agent-extra-socket
[2241 ms] Start: Run in container: /bin/sh
[2243 ms] Start: Run in container: echo ~
[2339 ms] /root
[2339 ms]
[2339 ms] Start: Run in container: cat <<'EOF-/tmp/vscode-remote-containers-d2c6586a65eeb453d225a77477381fa444f9822c.js' >/tmp/vscode-remote-containers-d2c6586a65eeb453d225a77477381fa444f9822c.js
[2342 ms]
[2342 ms]
[2342 ms] Start: Run in container: cat <<'EOF-/tmp/vscode-remote-containers-server-d2c6586a65eeb453d225a77477381fa444f9822c.js' >/tmp/vscode-remote-containers-server-d2c6586a65eeb453d225a77477381fa444f9822c.js_1663052957934
[2357 ms]
[2357 ms]
[2360 ms] Start: Run in container: # Test for /root/.gitconfig and git
[2376 ms]
[2376 ms]
[2376 ms] Start: Run in container: # Copy /Users/davidjackson/.gitconfig to /root/.gitconfig
[2379 ms]
[2379 ms]
[2379 ms] Start: Run in container: command -v git >/dev/null 2>&1 && git config --global --replace-all credential.helper '!f() { node /tmp/vscode-remote-containers-d2c6586a65eeb453d225a77477381fa444f9822c.js $*; }; f' || true
[2381 ms]
[2381 ms]
[2381 ms] Start: Run in container: # Test for /root/.ssh/known_hosts and ssh
[2383 ms]
[2383 ms]
[2383 ms] Start: Run in container: # Copy /Users/davidjackson/.ssh/known_hosts to /root/.ssh/known_hosts
[2401 ms]
[2401 ms]
[2413 ms] Start: Run in container: git clone --depth 1 --branch master https://github.com/wandb/devcontainer-test.git .
Cloning into '.'...
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 5 (delta 0), reused 4 (delta 0), pack-reused 0
Receiving objects: 100% (5/5), done.
[3696 ms] Start: Run in container: cat /workspaces/devcontainer-test/.devcontainer/devcontainer.json 2>/dev/null
[3699 ms] Start: Run in container: cat /workspaces/devcontainer-test/.devcontainer/devcontainer.json 2>/dev/null
[3703 ms] Start: Run in container: cat <<'EOF-/tmp/devcontainer-eab0e667-4c9c-4aa3-8684-f575bff6ba54.json' >/tmp/devcontainer-eab0e667-4c9c-4aa3-8684-f575bff6ba54.json_1663052959295
[3707 ms]
[3707 ms]
[3708 ms] Start: Run in container: docker volume ls -q
[4098 ms] Start: Run in container: docker ps -q -a --filter label=vsch.local.repository=https://github.com/wandb/devcontainer-test.git/tree/master --filter label=vsch.local.repository.volume=dc-test3 --filter label=vsch.local.repository.folder=devcontainer-test --filter label=vsch.quality=stable
[4229 ms] Start: Run in container: docker ps -q -a --filter label=vsch.local.repository=https://github.com/wandb/devcontainer-test.git/tree/master --filter label=vsch.local.repository.volume=dc-test3 --filter label=vsch.local.repository.folder=devcontainer-test
[4563 ms] Start: Run in container: node /root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js up --workspace-folder /workspaces/devcontainer-test --workspace-mount-consistency cached --id-label vsch.local.repository=https://github.com/wandb/devcontainer-test.git/tree/master --id-label vsch.local.repository.volume=dc-test3 --id-label vsch.local.repository.folder=devcontainer-test --log-level debug --log-format json --config /workspaces/devcontainer-test/.devcontainer/devcontainer.json --override-config /tmp/devcontainer-eab0e667-4c9c-4aa3-8684-f575bff6ba54.json --default-user-env-probe loginInteractiveShell --mount type=volume,source=dc-test3,target=/workspaces,external=true --mount type=volume,source=vscode,target=/vscode,external=true --skip-post-create --update-remote-user-uid-default off --mount-workspace-git-root true --terminal-columns 119 --terminal-rows 28
[4750 ms] remote-containers 0.251.0.
[4750 ms] Start: Run: docker buildx version
[4777 ms] Start: Resolving Remote
[4779 ms] Start: Run: git rev-parse --show-cdup
[4782 ms] Start: Run: docker ps -q -a --filter label=vsch.local.repository=https://github.com/wandb/devcontainer-test.git/tree/master --filter label=vsch.local.repository.volume=dc-test3 --filter label=vsch.local.repository.folder=devcontainer-test
[4811 ms] local container features stored at: /root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/node_modules/vscode-dev-containers/container-features
[4813 ms] Start: Run: tar --no-same-owner -x -f -
[4835 ms] Start: Run: docker -v
[4860 ms] Start: Run: docker events --format {{json .}} --filter event=start
[4862 ms] Start: Starting container
[4862 ms] Start: Run: docker run --sig-proxy=false -a STDOUT -a STDERR --mount type=volume,src=dc-test3,dst=/workspaces --mount type=volume,src=vscode,dst=/vscode -l vsch.local.repository=https://github.com/wandb/devcontainer-test.git/tree/master -l vsch.local.repository.volume=dc-test3 -l vsch.local.repository.folder=devcontainer-test --entrypoint /bin/sh davidwandb/devcontainer-pull-test -c echo Container started
Unable to find image 'davidwandb/devcontainer-pull-test:latest' locally
[3632 ms] Start: Run in container: git config remote.origin.fetch +refs/heads/*:refs/remotes/origin/*
[3799 ms] Start: Run in container: git fetch --unshallow --tags
remote: Enumerating objects: 13, done.
remote: Counting objects: 100% (13/13), done.
remote: Compressing objects: 100% (7/7), done.
remote: Total 10 (delta 3), reused 7 (delta 0), pack-reused 0
Unpacking objects: 100% (10/10), 837 bytes | 837.00 KiB/s, done.
From https://github.com/wandb/devcontainer-test
 * [new tag]         build-from-dockerfile     -> build-from-dockerfile
 * [new tag]         dockerfile-with-cachefrom -> dockerfile-with-cachefrom
docker: Error response from daemon: pull access denied for davidwandb/devcontainer-pull-test, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.
See 'docker run --help'.
[6491 ms] Start: Run: docker ps -q -a --filter label=vsch.local.repository=https://github.com/wandb/devcontainer-test.git/tree/master --filter label=vsch.local.repository.volume=dc-test3 --filter label=vsch.local.repository.folder=devcontainer-test
[6518 ms] Error: Command failed: docker run --sig-proxy=false -a STDOUT -a STDERR --mount type=volume,src=dc-test3,dst=/workspaces --mount type=volume,src=vscode,dst=/vscode -l vsch.local.repository=https://github.com/wandb/devcontainer-test.git/tree/master -l vsch.local.repository.volume=dc-test3 -l vsch.local.repository.folder=devcontainer-test --entrypoint /bin/sh davidwandb/devcontainer-pull-test -c echo Container started
[6518 ms] trap "exit 0" 15
[6518 ms] exec "$@"
[6518 ms] while sleep 1 & wait $!; do :; done -
[6519 ms]     at SF (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:257:2186)
[6519 ms]     at pb (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:257:2124)
[6519 ms]     at processTicksAndRejections (internal/process/task_queues.js:95:5)
[6519 ms]     at async DF (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:263:2006)
[6519 ms]     at async to (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:263:3110)
[6519 ms]     at async Ak (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:383:8108)
[6519 ms]     at async Ok (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:383:7864)
[6534 ms] Exit code 1
[6534 ms] Start: Run: docker rm -f 07bf0244dfb97948f953724132d94f5df9f00a4ebc00404b6b32720e65b21b4d
[6540 ms] Command failed: node /root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js up --workspace-folder /workspaces/devcontainer-test --workspace-mount-consistency cached --id-label vsch.local.repository=https://github.com/wandb/devcontainer-test.git/tree/master --id-label vsch.local.repository.volume=dc-test3 --id-label vsch.local.repository.folder=devcontainer-test --log-level debug --log-format json --config /workspaces/devcontainer-test/.devcontainer/devcontainer.json --override-config /tmp/devcontainer-eab0e667-4c9c-4aa3-8684-f575bff6ba54.json --default-user-env-probe loginInteractiveShell --mount type=volume,source=dc-test3,target=/workspaces,external=true --mount type=volume,source=vscode,target=/vscode,external=true --skip-post-create --update-remote-user-uid-default off --mount-workspace-git-root true --terminal-columns 119 --terminal-rows 28
[6540 ms] Exit code 1
[6609 ms] Container server terminated (code: 137, signal: null).
```

If you push the image to a public repository instead, the clone is successful.
