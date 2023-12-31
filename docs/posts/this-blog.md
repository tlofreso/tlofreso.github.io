---
draft: false
date: 2023-12-30
authors:
  - me
---

# This Blog
A couple years ago, I stumbled upon [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/){:target="blank"}. When the project added [blog support](https://squidfunk.github.io/mkdocs-material/blog/2022/09/12/blog-support-just-landed/?h=blog){:target="blank"} last September, I became a sponsor and started authoring away. Now _some 15 months later_ I'm finally getting around to configuring a proper build/deploy pipeline and actually getting this thing out the door. I've tried and abandoned personal blogging a number of times... hopefully, this time, it sticks.

<!-- more -->

I don't have much to share on how this site is hosted. I follow [this](https://squidfunk.github.io/mkdocs-material/publishing-your-site/?h=github#with-github-actions){:target="blank"} almost to a T. One obvious difference is the use of a custom domain. I got caught up for a bit getting this to work.

![GH Pages](../assets/posts/this-blog/gh-pages-dark.png#only-dark)
![GH Pages](../assets/posts/this-blog/gh-pages-light.png#only-light)

Basically, I'd configure the domain name `www.tlofreso.com` at `<my repo>/settings/pages --> Custom Domain`. This worked, until I would push an update to the site... then my custom domain settings would disappear. GitHub requires a `CNAME` file, which existed, but in the wrong location.

MkDocs has a command line utility: `mkdocs gh-deploy` specifically built for the purpose of deploying your docs to GitHub Pages ( Great! ). The utility creates, and pushes a `gh-pages` branch to your repo, which is ultimately what serves the site. The `CNAME` file I'd created at project root did not exist in the `gh-pages` branch. I moved it to my `docs_dir` per [the documentation](https://www.mkdocs.org/user-guide/deploying-your-docs/#custom-domains){:target="blank"}.

``` bash title="project_root/docs/CNAME" linenums="1"
www.tlofreso.com
```

Success!