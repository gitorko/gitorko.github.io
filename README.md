# gitorko blog

## Setup

```bash
git submodule add https://github.com/chipzoller/hugo-clarity themes/hugo-clarity
git submodule add https://github.com/martignoni/hugo-notice.git themes/hugo-notice
```

```bash
git clone https://github.com/gitorko/gitorko.github.io.git gitorko
cd gitorko
git checkout -b blog origin/blog
git submodule update --init --recursive
```

Start server
```bash
hugo
hugo server
```

```bash
hugo server -D
```

## Topics

* Open API design
* Spring jpa stored proc
* Sharding
