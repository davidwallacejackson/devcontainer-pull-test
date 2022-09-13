# Remote Containers pull auth bug repro

To reproduce this bug, you need a Docker Hub account with a private repository you can push to (or some other private image registry). I found it on an M1 Mac (OS 12.3.1) with Docker Desktop 4.11.1, VS Code 1.71.0, Remote Containers v0.251.0, Node 16.15.1 and `@devcontainers/cli@0.14.2`.

1. In `.devcontainer/devcontainer.json`, update `YOUR_DOCKERHUB` to refer to your own account or private registry. Commit and push.
2. In `.devcontainer/devcontainer.json`, uncomment lines 6-10
3. In Docker Hub or other registry, create a private repository called `devcontainer-pull-test`
4. From this directory, run `devcontainer build --workspace-folder . --image-name YOUR_DOCKERHUB/devcontainer-pull-test --push --buildkit` from the repository (again replacing `YOUR_DOCKERHUB`)
5. In VS Code, run `Remote-Containers: Clone Repository in Named Container Volume` and clone this repository. The repository fails to clone with output that looks something like this:

```
[20 ms] Remote-Containers 0.251.0 in VS Code 1.71.0 (784b0177c56c607789f9638da7b6bf3230d47a8c).
[20 ms] Start: Resolving Remote
[25 ms] Start: Check Docker is running
[25 ms] Start: Run: docker version --format {{.Server.APIVersion}}
[102 ms] Server API version: 1.41
[103 ms] Start: Run: docker volume ls -q
[168 ms] Start: Run: docker volume create --label vsch.local.repository=https://github.com/davidwallacejackson/devcontainer-pull-test.git/tree/master --label vsch.local.repository.unique=false --label vsch.local.repository.branch=master dc-test4
[248 ms] Start: Run: docker build -f /var/folders/wk/pbzbnm1j55nb32_8v0dvyr3w0000gn/T/vsch/bootstrap-image/0.251.0/bootstrap.Dockerfile -t vsc-volume-bootstrap /var/folders/wk/pbzbnm1j55nb32_8v0dvyr3w0000gn/T/vsch/bootstrap-image/0.251.0
[+] Building 0.3s (9/9) FINISHED
 => [internal] load build definition from bootstrap.Dockerfile             0.0s
 => => transferring dockerfile: 47B                                        0.0s
 => [internal] load .dockerignore                                          0.0s
 => => transferring context: 2B                                            0.0s
 => [internal] load metadata for mcr.microsoft.com/vscode/devcontainers/b  0.2s
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
[892 ms] Cloning Github repository: davidwallacejackson/devcontainer-pull-test into /workspaces/devcontainer-pull-test

[892 ms] Start: Run: docker run -d --mount type=volume,src=dc-test4,dst=/workspaces -v /var/run/docker.sock:/var/run/docker.sock vsc-volume-bootstrap sleep infinity
[1146 ms] Start: Run in container: /bin/sh
[1148 ms] Start: Launching Remote-Containers helper.
[1149 ms] ssh-agent: SSH_AUTH_SOCK in container (/tmp/vscode-ssh-auth-accb9ab985d23e06bdce0537063babc4990d4361.sock) forwarded to local host (/private/tmp/com.apple.launchd.4Cx3DcdyvD/Listeners).
[1149 ms] Start: Run: gpgconf --list-dir agent-extra-socket
[1151 ms] Start: Run in container: /bin/sh
[1154 ms] Start: Run in container: echo ~
[1274 ms] /root
[1274 ms]
[1274 ms] Start: Run in container: cat <<'EOF-/tmp/vscode-remote-containers-accb9ab985d23e06bdce0537063babc4990d4361.js' >/tmp/vscode-remote-containers-accb9ab985d23e06bdce0537063babc4990d4361.js
[1276 ms]
[1276 ms]
[1277 ms] Start: Run in container: cat <<'EOF-/tmp/vscode-remote-containers-server-accb9ab985d23e06bdce0537063babc4990d4361.js' >/tmp/vscode-remote-containers-server-accb9ab985d23e06bdce0537063babc4990d4361.js_1663053425430
[1288 ms]
[1289 ms]
[1290 ms] Start: Run in container: # Test for /root/.gitconfig and git
[1292 ms]
[1292 ms]
[1292 ms] Start: Run in container: # Copy /Users/davidjackson/.gitconfig to /root/.gitconfig
[1295 ms]
[1295 ms]
[1296 ms] Start: Run in container: command -v git >/dev/null 2>&1 && git config --global --replace-all credential.helper '!f() { node /tmp/vscode-remote-containers-accb9ab985d23e06bdce0537063babc4990d4361.js $*; }; f' || true
[1298 ms]
[1298 ms]
[1299 ms] Start: Run in container: # Test for /root/.ssh/known_hosts and ssh
[1300 ms]
[1300 ms]
[1300 ms] Start: Run in container: # Copy /Users/davidjackson/.ssh/known_hosts to /root/.ssh/known_hosts
[1318 ms]
[1318 ms]
[1355 ms] Start: Run in container: git clone --depth 1 --branch master https://github.com/davidwallacejackson/devcontainer-pull-test.git .
Cloning into '.'...
remote: Enumerating objects: 6, done.
remote: Counting objects: 100% (6/6), done.
remote: Compressing objects: 100% (5/5), done.
remote: Total 6 (delta 0), reused 3 (delta 0), pack-reused 0
Receiving objects: 100% (6/6), 4.33 KiB | 4.33 MiB/s, done.
[2516 ms] Start: Run in container: cat /workspaces/devcontainer-pull-test/.devcontainer/devcontainer.json 2>/dev/null
[2519 ms] Start: Run in container: cat /workspaces/devcontainer-pull-test/.devcontainer/devcontainer.json 2>/dev/null
[2523 ms] Start: Run in container: cat <<'EOF-/tmp/devcontainer-1c2e34ca-d9f8-49e3-aaa0-4d5a5fa9d247.json' >/tmp/devcontainer-1c2e34ca-d9f8-49e3-aaa0-4d5a5fa9d247.json_1663053426677
[2525 ms]
[2525 ms]
[2525 ms] Start: Run in container: docker volume ls -q
[2971 ms] Start: Run in container: docker ps -q -a --filter label=vsch.local.repository=https://github.com/davidwallacejackson/devcontainer-pull-test.git/tree/master --filter label=vsch.local.repository.volume=dc-test4 --filter label=vsch.local.repository.folder=devcontainer-pull-test --filter label=vsch.quality=stable
[3093 ms] Start: Run in container: docker ps -q -a --filter label=vsch.local.repository=https://github.com/davidwallacejackson/devcontainer-pull-test.git/tree/master --filter label=vsch.local.repository.volume=dc-test4 --filter label=vsch.local.repository.folder=devcontainer-pull-test
[3419 ms] Start: Run in container: node /root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js up --workspace-folder /workspaces/devcontainer-pull-test --workspace-mount-consistency cached --id-label vsch.local.repository=https://github.com/davidwallacejackson/devcontainer-pull-test.git/tree/master --id-label vsch.local.repository.volume=dc-test4 --id-label vsch.local.repository.folder=devcontainer-pull-test --log-level debug --log-format json --config /workspaces/devcontainer-pull-test/.devcontainer/devcontainer.json --override-config /tmp/devcontainer-1c2e34ca-d9f8-49e3-aaa0-4d5a5fa9d247.json --default-user-env-probe loginInteractiveShell --mount type=volume,source=dc-test4,target=/workspaces,external=true --mount type=volume,source=vscode,target=/vscode,external=true --skip-post-create --update-remote-user-uid-default off --mount-workspace-git-root true --terminal-columns 119 --terminal-rows 28
[3612 ms] remote-containers 0.251.0.
[3612 ms] Start: Run: docker buildx version
[3642 ms] Start: Resolving Remote
[3644 ms] Start: Run: git rev-parse --show-cdup
[3648 ms] Start: Run: docker ps -q -a --filter label=vsch.local.repository=https://github.com/davidwallacejackson/devcontainer-pull-test.git/tree/master --filter label=vsch.local.repository.volume=dc-test4 --filter label=vsch.local.repository.folder=devcontainer-pull-test
[3682 ms] local container features stored at: /root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/node_modules/vscode-dev-containers/container-features
[3683 ms] Start: Run: tar --no-same-owner -x -f -
[3704 ms] Start: Run: docker -v
[3728 ms] Start: Run: docker events --format {{json .}} --filter event=start
[3730 ms] Start: Starting container
[3731 ms] Start: Run: docker run --sig-proxy=false -a STDOUT -a STDERR --mount type=volume,src=dc-test4,dst=/workspaces --mount type=volume,src=vscode,dst=/vscode -l vsch.local.repository=https://github.com/davidwallacejackson/devcontainer-pull-test.git/tree/master -l vsch.local.repository.volume=dc-test4 -l vsch.local.repository.folder=devcontainer-pull-test --entrypoint /bin/sh davidwandb/devcontainer-pull-test -c echo Container started
Unable to find image 'davidwandb/devcontainer-pull-test:latest' locally
[2452 ms] Start: Run in container: git config remote.origin.fetch +refs/heads/*:refs/remotes/origin/*
[2619 ms] Start: Run in container: git fetch --unshallow --tags
remote: Enumerating objects: 12, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (6/6), done.
remote: Total 8 (delta 4), reused 4 (delta 0), pack-reused 0
Unpacking objects: 100% (8/8), 680 bytes | 680.00 KiB/s, done.
docker: Error response from daemon: pull access denied for davidwandb/devcontainer-pull-test, repository does not exist or may require 'docker login': denied: requested access to the resource is denied.
See 'docker run --help'.
[5193 ms] Start: Run: docker ps -q -a --filter label=vsch.local.repository=https://github.com/davidwallacejackson/devcontainer-pull-test.git/tree/master --filter label=vsch.local.repository.volume=dc-test4 --filter label=vsch.local.repository.folder=devcontainer-pull-test
[5224 ms] Error: Command failed: docker run --sig-proxy=false -a STDOUT -a STDERR --mount type=volume,src=dc-test4,dst=/workspaces --mount type=volume,src=vscode,dst=/vscode -l vsch.local.repository=https://github.com/davidwallacejackson/devcontainer-pull-test.git/tree/master -l vsch.local.repository.volume=dc-test4 -l vsch.local.repository.folder=devcontainer-pull-test --entrypoint /bin/sh davidwandb/devcontainer-pull-test -c echo Container started
[5224 ms] trap "exit 0" 15
[5224 ms] exec "$@"
[5224 ms] while sleep 1 & wait $!; do :; done -
[5224 ms]     at SF (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:257:2186)
[5224 ms]     at pb (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:257:2124)
[5224 ms]     at processTicksAndRejections (internal/process/task_queues.js:95:5)
[5224 ms]     at async DF (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:263:2006)
[5224 ms]     at async to (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:263:3110)
[5224 ms]     at async Ak (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:383:8108)
[5224 ms]     at async Ok (/root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js:383:7864)
[5235 ms] Exit code 1
[5235 ms] Start: Run: docker rm -f 421e310db8263700460d2424196fddc5ad7fa72be97c57f3dbbbf176da5acdee
[5241 ms] Command failed: node /root/.vscode-remote-containers/dist/dev-containers-cli-0.251.0/dist/spec-node/devContainersSpecCLI.js up --workspace-folder /workspaces/devcontainer-pull-test --workspace-mount-consistency cached --id-label vsch.local.repository=https://github.com/davidwallacejackson/devcontainer-pull-test.git/tree/master --id-label vsch.local.repository.volume=dc-test4 --id-label vsch.local.repository.folder=devcontainer-pull-test --log-level debug --log-format json --config /workspaces/devcontainer-pull-test/.devcontainer/devcontainer.json --override-config /tmp/devcontainer-1c2e34ca-d9f8-49e3-aaa0-4d5a5fa9d247.json --default-user-env-probe loginInteractiveShell --mount type=volume,source=dc-test4,target=/workspaces,external=true --mount type=volume,source=vscode,target=/vscode,external=true --skip-post-create --update-remote-user-uid-default off --mount-workspace-git-root true --terminal-columns 119 --terminal-rows 28
[5241 ms] Exit code 1
[5313 ms] Container server terminated (code: 137, signal: null).
```

If you push the image to a public repository instead, the clone is successful.
