# ETH Zurich - Computer Science

Welcome! This repository is dedicated to a number of documentation pages which are/were written during my journey at
ETH Zurich. Those could be useful for future projects which build on that knowledge.

The documentation is primarily dedicated to computer science masters courses. However, a series of bachelors courses
are included as well.

## How to use

This documentation is building on [mkdocs](https://www.mkdocs.org/) using the [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) theme.
To test the website locally on your computer, use `mkdocs serve` after moving to the working directory.
This will create a live-reloading docs server which is perfect for testing your ongoing changes. 

Once everything is ready, use `git push`. This will invoke a ci pipeline. Pages are built onto a separate branch of your repository. 
If activated, the page will then be available on `[username].github.io/[repo-name]`.

To get it up and running, a couple of packages need to be installed. Use the following command to set it all up.
```shell
sudo snap install drawio
pip install mkdocs-material mkdocs-git-revision-date-localized-plugin mkdocs-drawio-exporter
```


## Work in progress!
As for now, this page is by no means complete. It is constantly being worked on and more content will be available soon. 