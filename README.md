# Overview
This repository contains documentation for projects under [Konveyor.io](http://konveyor.io)
* Leverages [Hugo](https://gohugo.io/)
* Is served at: [https://konveyor.github.io/](https://konveyor.github.io/)
    * Assets are built and served from the branch [gh-pages](https://github.com/konveyor/konveyor.github.io/tree/gh-pages)

# Getting Started, how to run locally
1. Fork or Clone the repository, ensure you check out the configured [git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
    * Example:

        ``` git clone https://github.com/konveyor/konveyor.github.io.git --recursive```
            
        * We are using the ```--recursive`` command line flag to automatically clone a few themes which Hugo will use.  These are leveraging [git submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules) 
2. ```cd konveyor.github.io```
3. ```hugo server -D```
    * Run the hugo server locally, building posts that may be in 'draft'
4. Visit http://localhost:1313/ in your web browser

# How changes get published to 'production'

All changes which merge to 'main' will [trigger a GitHub Action](https://github.com/konveyor/konveyor.github.io/actions) to run that builds the hugo assets and publishes to the [gh-pages branch](https://github.com/konveyor/konveyor.github.io/tree/gh-pages) 

The URL,[https://konveyor.github.io/](https://konveyor.github.io/) is configured to serve the web assets from the [gh-pages branch](https://github.com/konveyor/konveyor.github.io/tree/gh-pages)  