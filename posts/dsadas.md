---
title: "dsadas"
date: ""
excerpt: "dasdasdas"
category: ""
tags: []
featured: false
bannerImage: ""
---

**GET /repos/loartdev/blogs/contents/posts%[2Ftesting-post.md](http://2Ftesting-post.md)?ref=main - 404 with id F270:18F81D:3FEC86:DE8CE1:6A616F1A in 118ms**

lib\\github\\read.ts (17:22) @ getFileOnRef

```
  15 |   const octokit = getOctokit();
```

  16 |   try {

&gt; 17 |     const { data } = await [octokit.rest](http://octokit.rest).repos.getContent({

     |                      ^

  18 |       owner: githubConfig.owner,

  19 |       repo: githubConfig.repo,

  20 |       path,

**Call Stack17**

Show 9 ignore-listed frame(s)

**getFileOnRef**

lib\\github\\read.ts (17:22)

**Promise.all**

&lt;anonymous&gt;

**&lt;anonymous&gt;**

lib\\github\\read.ts (109:58)

**Promise.all**

&lt;anonymous&gt;

**Promise.all**

&lt;anonymous&gt;

**aggregatePostList**

lib\\github\\read.ts (139:31)

**BlogPage**

app\\(dashboard)\\blog\\page.tsx (11:17)

**BlogPage**