---
weight: 3
bookFlatSection: true
title: "Writing Blog Posts for Mwmbl"
date: "2026-10-30"
lastmod: "2026-10-30"
---

# Writing Blog Posts for Mwmbl

Writing a blog post is a fantastic way to help promote Mwmbl. We are doing a lot
of work that doesn't get recorded in the blog. The work is often communicated on
Matrix to our small community, but when it's in the blog the wider world can
learn what we are doing.

On the days when we write a blog post and put it on Hacker News we see a
noticeable uptick in the number of registered users. Occasionally, a post will
go viral and we will get hundreds of new users. So writing a post can make a
real difference to the success of the project.

## What you'll need

To be accepted as a Mwmbl blog writer:

 - You need to care about Mwmbl. Posts that are purely for self-promotion will
   not be accepted.
 - You need to be a good writer. You can use LLMs for research and checking your
   post, but not for writing itself.
 - You need to know how to use git and GitHub to fork the repository, commit
   your post and open a pull request.
 - You need to know how to use markdown to format your post and include links.

If you can do all these things, you're in!

## Before you start

Before you start writing, please join [our Matrix Space](https://matrix.to/#/#mwmbl:matrix.org)
and let us know you'd like to write a post. If you already have something you
feel inspired to say, great! let us know. If not, check the [blog issues](https://github.com/mwmbl/blog/issues/)
for something that inspires you.

## Setting up

The blog is a [Hugo](https://gohugo.io/) site that lives in the
[mwmbl/blog](https://github.com/mwmbl/blog) repository. To get started,
[fork the repository](https://github.com/mwmbl/blog/fork) on GitHub, then clone
your fork:

```bash
git clone git@github.com:<your-username>/blog.git
cd blog
git checkout -b my-post-name
```

Posts live in `content/articles/`, one markdown file per post. The file name
becomes the URL, so use a short, lowercase, hyphenated name, e.g.
`content/articles/reranking-on-the-client-side.md` is published at
`https://blog.mwmbl.org/articles/reranking-on-the-client-side/`.

Each post starts with front matter like this:

```yaml
---
title: "Tracking user IDs in the crawler"
date: "2026-05-22"
author: ["Your Name"]
---
```

If your post includes images, put them in `static/images/` and reference them
from your post with a path starting at `/images/`, for example:

```markdown
![A short description of the image](/images/my-image.jpg)
```

Please keep images reasonably small (a few hundred KB at most) and always
include a description for accessibility.

To preview your post, [install Hugo](https://gohugo.io/installation/) and run
this from the root of the repository:

```bash
hugo server
```

Then open [http://localhost:1313](http://localhost:1313) in your browser. The
page will reload automatically as you edit.

## How to write your post

Feel free to use LLMs to understand the topic you are writing about. When it
comes to the actual writing, you need to use your own words. People can tell
when something is written by an LLM, and often they will just stop reading. We
want to hear the voice of real people.

Look at other posts in the repository for examples of how to format and write
your posts.

When you're done, you can use an LLM to check it for accuracy and typos if you
wish.

## Submitting your post

When your post is ready, commit it along with any images and push the branch to
your fork:

```bash
git add content/articles/my-post-name.md static/images/
git commit -m "Add post on <topic>"
git push -u origin my-post-name
```

Then go to [mwmbl/blog on GitHub](https://github.com/mwmbl/blog) and open a pull
request from your branch. If the post is related to one of the
[blog issues](https://github.com/mwmbl/blog/issues/), mention it in the pull
request description. Then send a message on Matrix to let us know it's ready for
review.

We may ask you to make changes. To update your pull request, just commit and
push to the same branch again. Once we're happy with it, we'll merge it and your
post will be published!

## After you've posted

Feel free to post on socials, but please follow best practices for each platform
and don't spam as it gives us a bad reputation.

Thanks for supporting Mwmbl! Your contribution makes a real difference.
