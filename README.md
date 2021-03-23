# C & C++ FAQ

An open source FAQ for C and C++, available at (temporary URL) [https://64.github.io/cpp-faq](https://64.github.io/cpp-faq).

⚠️ **This project is heavily WIP**: don't expect links here to work for long. Bugfixes and new FAQ entries are most welcome - see [Contributing](#Contributing).

## Contributing

First [install `hugo`](https://gohugo.io/getting-started/installing#readout).

### Adding new FAQ articles

First, please read [`STYLE.md`](STYLE.md) to familiarize yourself with the style and tone in which articles should be written.

Run `hugo new faq/my-faq-post.md`. This generates a file named `content/faq/my-faq-post.md` with the appropriate template. Add the content (in markdown format) below the bottom `---`. Hugo will be able to locate your content without any additional work.

## Building

1. [Install `hugo`](https://gohugo.io/getting-started/installing#readout).
2. Run `git submodules update --init` to download the theme if you haven't already
3. Run `hugo server -D` then navigate to [http://localhost:1313/cpp-faq/](http://localhost:1313/cpp-faq/) in your browser.
