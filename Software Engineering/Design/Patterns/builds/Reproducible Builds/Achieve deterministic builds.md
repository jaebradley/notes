# Achieve deterministic builds

## [Volatile inputs can disappear](https://reproducible-builds.org/docs/volatile-inputs/)

* Inputs from the network are volatile and it's best to make a build system not rely on remote data
* If you have to rely on remote data, ensure integrity using cryptographic checksums and keep backups

## [Stable order for inputs](https://reproducible-builds.org/docs/stable-inputs/)

* When processing inputs, make sure the order of processing the inputs is stable 
* For example, listing files in a directory should occur in the same order
* When sorting inputs, ensure that the sort order is not affected by the system locale settings ans some locales do not distinguish betwee uppercase and lowercase characters
  * In the following example [the `LC_ALL=C` overrides all other localization settings](https://unix.stackexchange.com/questions/87745/what-does-lc-all-c-do)

```bash
find src -print0 | LC_ALL=C sort -z | tar --no-recursion --null -T - -cf archive.tar
```
