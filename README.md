# gitorko blog

## Setup

Build public files

```bash
hugo
cd public
git commit -am "code"
git push origin main
```

Update Theme

```bash
hugo mod clean
hugo mod get -u github.com/chipzoller/hugo-clarity
```

Update all hugo modules
```bash
hugo mod get -u ./...
hugo mod tidy
```

Old way of adding git submodule

```bash
git submodule add -b main https://github.com/gitorko/gitorko.github.io.git public
git submodule update --init --recursive
```

Start server
```bash
hugo server
```

```bash
hugo new content post/new-post.md
hugo server -D
```

## Topics

* Open API design
* Spring jpa stored proc
* Sharding
