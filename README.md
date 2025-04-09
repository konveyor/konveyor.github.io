# Overview
This is the repository for [Konveyor.io](http://konveyor.io). It serves as a central location for the website, documentation and blog. This site is built using [Hugo](https://gohugo.io/) with [docsy](https://www.docsy.dev/docs/) theme and is hosted on [Netlify](https://www.netlify.com/).

## Contributing

### Prerequisites
Your system needs to have the following installed to be able to build this website:

- [Node.js](https://nodejs.org/en/about/releases/) (Latest LTS version recommended)
- [Git](https://git-scm.com/)
- [Go](https://go.dev/dl/)
- [Hugo](https://github.com/gohugoio/hugo/releases) (extended version required for Docsy - version 0.110.0 or later) 

### Cloning the repository

1. [Fork this repository](https://github.com/konveyor/konveyor.github.io/fork) into your own GitHub account.
1. Clone the site to your local machine:  
   `git clone git@github.com:your-username/konveyor.github.io.git`.
   `cd konveyor.github.io`
1. Add the upstream remote to follow this repository's changes:  
   `git remote add upstream git@github.com:konveyor/konveyor.github.io.git`  
   `git fetch upstream`

### Building the site locally
1. In this project, the Docsy theme is pulled in as a Hugo module, together with its dependencies:
    ```
    hugo mod graph
    ```
2. If you want to do SCSS edits and want to publish these, you need to install PostCSS and other packages in package.json:
   ```
   npm install
   ```
3. Run the website locally, building posts that may be in 'draft'
   ```
   hugo server -D
   ```
4. Visit http://localhost:1313/ in your web browser

## Content types

### Docs
Docs can be found under `content/docs/`.

### Blog posts

Blog posts can be found under `content/blog/:year` where `:year` indicates the publish year. To create a new post:

1. Create a new directory. The name of the directory will be the base of the URL. Use lowercase letters and hyphens only — no upppercase or other punctuation allowed.

2. In that directory create a file called `index.md`. The file have a frontmatter section containing various fields and the [markdown](https://www.markdownguide.org/basic-syntax/) section containing the actual blog post.

3. Fill out the frontmatter fields. Example frontmatter:

```
---
author: tquinn
date: 2021-03-03T07:00:00Z
description: >-
  When we started providing professional services around enterprise Kubernetes, it became clear we needed a program-level framework for adopting containers that spelled out the activities of multiple project teams.
featured: true
image: banner.jpg
tags:
  - Metrics
  - OpenShift
title: Exploring a Metrics-Driven Approach to Transformation
---
```

The fields are:

- **author** — the author identifier. _Note: author field not currently used._
- **date** — The publication date of the post. Must follow the above format.
- **description** — The description will be used when the post is shared to social media.
- **featured** — Should this post be displayed under Featured Posts? Use sparingly. It makes no sense to have every post be a featured post.
- **image** — This is the main image of the post and the image that will be used on social media shares. Place image in the post directory.
- **tags** — One tag per line, titlecase. Less is more with tags. These are only used to link to related posts. They are _not_ SEO keywords. Do not attempt to come up with as many tags as you can think of and all of their synonyms. No, seriously, don't do it.
- **title** — Title of the post.

### Announcements
An announcement is a _regular Hugo page_ contained under the `announcements` section. This means that we leverage Hugo's builtin handling of page dates (future or expired) and weights to automatically show or hide banners depending on the build date and determine banner ordering.

> Announcements are currently used as banners only.

#### Creating an announcement
To add a new announcement, create an announcement markdown file under the
`announcements` folder using the following command:

```sh
hugo new --kind announcement content/announcements/announcement-file-name.md
```

Adjust according to your desired file name. Add the announcement text as the body of the page.

> For banners, the announcement body should be a short phrase.

#### Announcement list

Any given announcement will appear in a site build when the build date falls
between the `date` and `expiryDate` fields of the announcement.

Announcements will appear in the standard page order as determined using Hugo's
[Regular pages](https://gohugo.io/methods/site/regularpages/) function. That is,
the "lightest" announcements (by `weight`) will appear first; when weights are
the same or unspecified, the most recent announcements (by `date`) will appear
first, etc.

So, if you want to force an announcement to the top, use a negative `weight` in
the front matter.

## Configuration
Site-wide configuration details and parameters are defined in `config.toml`. These include theme config (Docsy, of course!), project name, community supporters, homepage section data (konveyor components), Google Analytics configuration, and more.

## How changes get published to 'production'
All changes which merge to 'main' will [trigger a GitHub Action](https://github.com/konveyor/konveyor.github.io/actions) to run that builds the hugo assets and publishes to the Netlify. 


## Code of Conduct
Refer to Konveyor's Code of Conduct [here](https://github.com/konveyor/community/blob/main/CODE_OF_CONDUCT.md).

## DCO
Licensing is important to open source projects. It provides some assurances that
the software will continue to be available based under the terms that the
author(s) desired. We require that contributors sign off on commits submitted to
our project's repositories. The [Developer Certificate of Origin
(DCO)](https://probot.github.io/apps/dco/) is a way to certify that you wrote and
have the right to contribute the code you are submitting to the project.

You sign-off by adding the following to your commit messages. Your sign-off must
match the git user and email associated with the commit.

    This is my commit message

    Signed-off-by: Your Name <your.name@example.com>

Git has a `-s` command line option to do this automatically:

    git commit -s -m 'This is my commit message'

If you forgot to do this and have not yet pushed your changes to the remote
repository, you can amend your commit with the sign-off by running 

    git commit --amend -s 
