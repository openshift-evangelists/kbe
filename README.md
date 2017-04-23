# KBE site code

References:

- https://gohugo.io/overview/configuration/
- https://gohugo.io/overview/quickstart/
- https://gohugo.io/tutorials/github-pages-blog/


To publish:

```bash
# build the content in public/ folder:
hugo --theme=beautifulhugo
# add generated content (lives in the gh-pages branch):
cd public/
git add --all
git commit -m "publishes site"
git push -f origin gh-pages
```
