# gpk

Git based, decentralized and secure package management for JavaScript and
Node.js libraries and applications.

## Features

- Packages are decentralized via Git and can be hosted and published
  via any Git repository.
- Signature verification via signed Git tags.
- Deterministic flat installation of packages to support commiting
  and bundling dependencies with Git.
- Resolves shared dependencies based on semantic versioning via Git
  tags (e.g. `v1.1.0`).
- Node.js C/C++ addon build support with `node-gyp`.
- Exclude files from a package with `.npmignore` and `.gitignore`.

## Install

Clone and verify:
```
git clone https://github.com/braydonf/gpk
cd gpk
git verify-commit HEAD
```

Install globally:
```
./bin/gpk install -g
```

## Usage

### Command examples

- `gpk install` Install dependencies and verify signatures.
- `gpk install https://<url>/<org>/<repo>` Install latest tag and add a dependency.
- `gpk install https://<url>/<org>/<repo>#<branch>` Install from a specific branch.
- `gpk install -g https://<url>/<org>/<repo>` Install a global module.
- `gpk install -g` Link a module globally.
- `gpk test` Run package tests.
- `gpk run <script>` Run package defined script.
- `gpk rebuild` Build native addons.
- `gpk uninstall <name>` Uninstall and remove a dependency.
- `gpk uninstall -g <name>` Uninstall a global module.
- `gpk init` Initialize a package.
- `gpk help` Display all available commands.

### Specifying dependencies

Here is how to specify dependencies in `package.json`:

```json
{
  "dependencies": {
    "bcoin": "git+https://github.com/bcoin-org/bcoin.git#semver:~2.0.0",
    "bcrypto": "git+https://github.com/bcoin-org/bcrypto.git#semver:~4.2.6",
    "bmultisig": "git+https://github.com/bcoin-org/bmultisig.git#semver:~2.0.0",
    "buffer-map": "git+https://github.com/chjj/buffer-map.git#semver:~0.0.7",
    "n64": "git+https://github.com/chjj/n64.git#semver:~0.2.10"
  }
}
```
The signature of the matching Git tag or commit is verified for each
dependency. The dependencies must be from a Git repository. The referenced
Git tag or commit must be signed and the necessary public keys imported.

You can also use `gpk` specific shorthand in `package.json`:

```json
{
  "remotes": {
    "bcoin-org": "git+https://github.com/bcoin-org/",
    "chjj": "git+https://github.com/chjj/"
  },
  "dependencies": {
    "bcoin": "bcoin-org:bcoin.git#semver:~2.0.0",
    "bcrypto": "bcoin-org:bcrypto.git#semver:~4.2.6",
    "bmultisig": "bcoin-org:bmultisig.git#semver:~2.0.0",
    "buffer-map": "chjj:buffer-map.git#semver:~0.0.7",
    "n64": "chjj:n64.git#semver:~0.2.10"
  }
}
```

And specify Git branches:

```json
{
  "remotes": {
    "bcoin-org": "git+https://github.com/bcoin-org/",
  },
  "dependencies": {
    "bcoin": "bcoin-org:bcoin.git#pkg-dependencies",
  }
}
```

### Configuration

To customize the global installation path, use the
`PREFIX` environment variable, for example:

```sh
export PREFIX=/home/user/global_node_modules
```

And then add that to your path, for example:
```sh
export PATH="$PREFIX/bin:$PATH"
```

This may be necessary if the installation of Node.js
was via an operating system package manager or otherwise
installed at `/usr/bin` or another path that requires
root or admin permissions.

## Testing

Import the key used for signing:

```sh
gpg --recv-keys "5B7D C58D 90FE C1E9 90A3  10BA F24F 232D 108B 3AD4"
```

And run the tests:
```sh
gpk test
```

For detailed logging when running tests you can use
the environment variable `TEST_LOG`, for example:

```sh
TEST_LOG=true gpk test
```

Test data in created in a temporary directory, using
this format: `/tmp/gpk-test-<name>-<id>/`.
