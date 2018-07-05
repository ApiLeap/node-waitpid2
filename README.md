# node-waitpid2

*This package is needed to clean up zombie processes for the node-renderer*

Originally forked from AvianFlu/node-waitpid changing the behaviour to be more like nature intended.

The `waitpid` syscall is used to [wait for a process to change state](https://linux.die.net/man/2/waitpid).

For terminated child processes, waiting for them releases the resources associated with the child in the process table. If you don't do this, they could remain zombies for ever!

You should implement a blocking loop in your application in order to wait for a child process. You should do this asyncronously by passing `WNOHANG` as an option and using a timeout to call `waitpid` until the PID returned is `0` (no child process state changes left). Don't let the `waitpid` call block the whole thread.

Starting from version 2.0.0 fixed to use the latest node addon/v8 API.

## building binary

If a binary is available it will be installed using `node-pre-gyp`. (node-pre-gryp)[https://github.com/mapbox/node-pre-gyp] is a command line tool that helps to install C++ modules into a binary. *WARN: It is OS dependent*.

My suggestion is to build using the same OS as you intended to use your lambda with. Lambci provides excellent ones. Eg:
```
$ docker run --rm -v "$PWD:/var/task" --user root  --entrypoint bash -it lambci/lambda:nodejs8.10
$ yum install gcc-c++ -y
```

Binaries are hosted in this repository releases page. The process of building and uploading is as follows:

```
export NODE_PRE_GYP_GITHUB_TOKEN=<token>
./node_modules/node-pre-gyp/bin/node-pre-gyp configure
./node_modules/node-pre-gyp/bin/node-pre-gyp build
./node_modules/node-pre-gyp/bin/node-pre-gyp package
./node_modules/node-pre-gyp-github/bin/node-pre-gyp-github.js publish
```

To get the `NODE_PRE_GYP_GITHUB_TOKEN`:

1. In your GitHub account, go to Settings
1. Click `Developer settings`
1. Click `Personal access tokens`
1. Click `Generate new token`
1. Select `public_repo` and `repo_deployment`
1. Click `Generate Token`
1. Copy the key that's generated and set `NODE_PRE_GYP_GITHUB_TOKEN` environment variable to it. Within your command prompt:

Go to github and release the version.
