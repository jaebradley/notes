# [The Three Pillars of Reproducible Builds](https://fossa.com/blog/three-pillars-reproducible-builds/)

* Reproducible build produces same byte-for-byte output no matter what computer you run on, what time you run, and what external services are accessible from the network

## Repeatable builds

* Given that our build inputs are available and the world doesn't change at all around us, does the build produce the same output?
* Deterministic dependency install plans (like a lock file)
* Compilation can secretly introduce non-determinism like
  * Build steps that read files outside of version control
  * Build steps that use system time to generate timestamps
  * Build steps that download dependencies from the network that are not expressed in the install plan (NPM dependency that downloads a cached binary build of its C bindings from GitHub)
  * Builds that change behavior based on currently set environment variables but don't commit environment variable configuration

## Immutable Environments

* Container runtimes like Docker help ensure that everyone is using the same set of system dependencies, environment variables, filesystem
* Running `apt install nodejs` will give you a different answer today than it did last year which is why Dockerfiles themselves do not describe a reproducible build
  * Running the same Dockerfile at a different point in time will produce different build outputs
* Configure builds to specify exact versions (i.e. exact content hashes) so that a build in the future will use the same version as a build today

## Source Availability

* Registries can go down, so only reliable way to mitigate this is to run package registry mirror (i.e. build pipeline's availability is only as good as the availability of external service)
* Another simple way to achieve maximal availability is to vendor them, which means storing the dependency source code in version control (i.e. committing `node_modules` to source control)
