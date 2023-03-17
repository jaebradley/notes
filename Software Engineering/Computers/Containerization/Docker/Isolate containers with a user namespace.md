# [Isolate containers with a user namespace](https://docs.docker.com/engine/security/userns-remap/)

* Linux namespaces provide isolation for running processes, limiting their access to system resources without the running process being aware of the limitations
* The best way to prevent privilege-escalation attacks from within a container is to configure your container's application to run as unprivileged users
* For containers whose processes must run as the `root` user within the container, you can re-map this "container root" user to a less-privileged user on the Docker host
  * This less-privileged user is assigned a range of UIDs which function within the namespace as normal UIDs, but have no privileges on the host machine itself

## About remapping and subordinate user and group IDs

* `/etc/subuid` is concerned with the user ID range and `/etc/subgid` is concerned with the group ID range
* `someuser:231072:65536` - `someuser` is assigned a subordinate user ID range of `231072` and the next ``65536` integers in the sequence
  * UID `231072` is mapped within the namespace (i.e. container) as UID `0` and UID `231072` is maaed as UID `1` and so forth
  * If a process attempts to escalate privilege outside of the namespace, the process is running as an unprivileged high-number UID on the host, which does not even map to a real user, which means that the process has no privileges on the host system
* Ranges for different users must not overlap so that a process cannot gain access in a different namespace - on Linux, system utilities manage the ranges for you when you add or remove users

## Prerequisites🔗

* The subordinate UID and GID ranges must be associated with an existing user
* The user owns the namespaced storage directories under `/var/lib/docker/`
* If you don't want to use an existing username or user ID, Docker can create one for you

## Enable userns-remap on the daemon

* Start `dockerd` with the `userns-remap` flag or configure the `daemson.json` file
* Verify that previous images are not available using the `docker image ls` command
* Start a container from the previous image
* Verify that a namespaced directory exists within `/var/lib/docker/` named with the UID and GID of the namespaced user, is owned by that UID and GID, and not group/world readable
