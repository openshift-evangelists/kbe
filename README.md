# KBE site code

This is the source code for `kubernetesbyexample.com` using [Hugo](https://gohugo.io)
as the engine.

## Contribute

To contribute, please either raise an [issue](https://github.com/mhausenblas/kbe/issues)
describing what you want to see covered here or send in a PR to the `master` branch.
If you plan to contribute content, check out [content/page/](content/page/)
for the content in Markdown and [specs/](specs/) for respective YAML specifications.

## Build locally

1. Install `hugo` by referring [installation guide](https://gohugo.io/overview/installing)
1. Get your local preview by running following command in top-level dir:

```bash
hugo server --theme=beautifulhugo --buildDrafts
```

## Publish

For site admins only, requires push access to this repo.

```bash
# still in top-level dir build the content in public/ dir:
hugo --theme=beautifulhugo
# add generated content (which lives in the gh-pages branch):
cd public/
git add --all
git commit -m "publishes site"
git push -f origin gh-pages
```

## References

- https://themes.gohugo.io/beautifulhugo/
- https://gohugo.io/overview/configuration/
- https://gohugo.io/overview/quickstart/
- https://gohugo.io/tutorials/github-pages-blog/
