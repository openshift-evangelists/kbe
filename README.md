# KBE site code

Local preview (in top-level dir):

```bash
hugo server --theme=beautifulhugo --buildDrafts
```

To publish:

```bash
# still in top-level dir build the content in public/ dir:
hugo --theme=beautifulhugo
# add generated content (lives in the gh-pages branch):
cd public/
git add --all
git commit -m "publishes site"
git push -f origin gh-pages
```

References:

- https://gohugo.io/overview/configuration/
- https://gohugo.io/overview/quickstart/
- https://gohugo.io/tutorials/github-pages-blog/
