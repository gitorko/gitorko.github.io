# gitorko blog

## Setup

Checkout code

```bash
git clone https://github.com/gitorko/gitorko.github.io.git gitorko
cd gitorko
git checkout -b blog origin/blog
git submodule update --init --recursive
```

Install the tools (macOS). The site builds with the latest Hugo (v0.170.1 at the
time of writing, also pinned in `.github/workflows/main.yml`) and needs Dart Sass
to compile the theme's stylesheets:

```bash
brew install hugo dart-sass
hugo version
```

On other platforms, download Hugo from
https://github.com/gohugoio/hugo/releases and install Dart Sass from
https://sass-lang.com/install so that `sass` is on the PATH.

Start server

```bash
hugo server
hugo server --ignoreCache
hugo server --cacheDir /tmp
```

Production build (what the GitHub Actions workflow runs)

```bash
hugo --gc --minify
```

### Upgrading Hugo

The site was originally built with Hugo 0.125.4 and migrated to current Hugo:

* Shortcodes live in `layouts/_shortcodes/` as `.html` templates (Hugo 0.146+
  no longer finds `layouts/shortcodes/*.md` for HTML pages). `ghcode` and
  `markcode` use `try` around `resources.GetRemote`, because remote fetch
  errors now fail the build directly.
* `config/_default/hugo.toml` uses `[pagination] pagerSize` and `locale`;
  `languages.toml` uses `label`.
* The `hugo-clarity` theme submodule is updated to its latest `master`. Its
  `_components.sass` contains two tab-indented lines that newer Dart Sass
  rejects, so a fixed copy is kept in `assets/sass/_components.sass`, which
  overrides the theme file. Delete that copy if the theme fixes it upstream.
* A `ghcode` link to a file that no longer exists in the source repository
  fails the whole build, so keep those links pointing at existing files.
* Hugo may still print a deprecation warning for `.Site.LanguageCode`; it comes
  from the theme and is harmless until the theme is updated.

## Topics

* Open API design
* Spring jpa stored proc
* Sharding
