# [How I stopped worrying and loved Makefiles](https://gagor.pro/2024/02/how-i-stopped-worrying-and-loved-makefiles/)

```make
.PHONY: reload

all: aliases.db access.db virtual.db reload

aliases.db: aliases
    postalias aliases

access.db: access
    postmap access

virtual.db: virtual
    postmap virtual

reload:
    postfix reload

```

* `.PHONE: reload` - `reload` is a phony target, a target that does not represent an actual file
  * Typically used for targets that don't produce output files (`clean`, `all`, etc)
  * Even if a file named `reload` exists in the directory, the `reload` target will still be executed
* The `all` target means that the `aliases.db`, `access.db`, and `virtual.db` files will be generated, and then the `reload` target will be executed
* The `aliases.db` target depends on the file `aliases`
  * If the `aliases` file is newer than the `aliases.db` file or the `aliases.db` file does not exist, the related commands will be executed
