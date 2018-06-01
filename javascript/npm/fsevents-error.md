# `fsevents` error

![alt](https://imgur.com/kofM2Jb.png)

I ran into this error when I was trying to `npm install` my [`github-personal-access-token-generator-cli`](https://github.com/jaebradley/github-personal-access-token-generator-cli).

The solution was to

```bash
npm install fsevents@latest
npm install
npm uninstall fsevents@latest
npm install
```

Something about the order of installs was probably causing the first installed `fsevents` to be the wrong version (i.e incompatible with node 10).