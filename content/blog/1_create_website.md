---
title: Create 11ty static website from template
description: This is a post describing how I started this website
permalink: "/blog/create-website/"
date: 2023-11-05
templateEngineOverride: md
tags:
  - 11ty
  - GitHub Pages
---

### Introduction

In this post, I provide an overview of how I used the 11ty base theme as the starting point of this blog.

### Pre Requisites

To authorize requests to github when running commands from terminal e.g. cloning, git push/pull etc, I am using SSH keys. Ensure that you have configured an SSH key in GitHub.

To have the same setup on your end, you can follow these guides from the GitHub documentation:

* [Generate an SSH key](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)
* [Add a new SSH key to your GitHub account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)

I also recommend using VS Code as your code editor. Feel free to use any code/text editor of your choice.

### Create Repository from Template

The template I used for this site is the [eleventy base blog](https://github.com/11ty/eleventy-base-blog). 

Using the link above to view the eleventy-base-blog in github.

![use template eleventy base blog](/img/create_website_blog/1_11ty_base_blog.png)

Select ***Use this template*** &rarr;  ***Create a new repository***

On the ***Create a new repository*** page, specify a name for your name repository. Then select ***Create repository***.

![create new repository from template](/img/create_website_blog/2_create_repository.png)

### Clone repository and run server locally

On your local machine, clone the newly created repository in a directory on your choice. 

Command to clone respository to your local machine: **`git clone git@github.com:<USER-NAME>/<REPOSITORY-NAME>.git`**

Before we begin modifying the code, first, we shall run the server locally. Run **`npm install`** to install the depdencies.

Thereafrer, to run the server locally, run **`npm start`**.

### Update code base

In package.json, update the pathprefix seen in the **build-ghpages** script to be the same as your respository name:

```
"build-ghpages": "npx @11ty/eleventy --pathprefix=/noname-blog/"
```

Thereafter, remove **package-lock.json** from .gitignore.

In the root of your repository, create a folder named **.github**. Then, inside that folder, create a sub-directory named **workflows**.

Using the YAML below, create the file ***.github/workflows/build-and-deploy.yml*** in the newly created directory. 

```yaml
name: Build and Deploy
on: [push]
jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v3

      - name: Install and Build
        run: |
          npm ci
          npm run build-ghpages

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          publish_dir: ./_site
          github_token: ${{ secrets.GITHUB_TOKEN }}
```

### Deploy Code

After making the above changes, deploy your code:

```sh
git add -A
git commit -m "Update package.json. Add build file."
git push
```

After running the above, navigate to your github respository on github.com. You should then see a red x next to the short code of your most recent commit. You may need to wait for a few more minutes to see this.

The red x indicates that there was an issue with your most recent build. Select the red x to see more details. 

![Build failure about code push](/img/create_website_blog/4_check_build_result.png)

When you select **Details**, this will take you to the Actions tab, and provide more details regarding why the build failed. In this case, a 403 error was being returned.

![Build failure about code push](/img/create_website_blog/5_build_failed.png)

### Update Permissions

To resolve the error, you will need to go to Settings &rarr;  Action &rarr; General  &rarr;  Workflow permissions  &rarr; choose **read and write permissions** [1].

Thereafter, re-run your failed build job. To do this, go to Actions, and under All workflow, you should see the build that failed. This would have the same value/name used in your commit message. In my case, this was **Update package.json. Add build file**. The name of the workflow that run would be the same as the commit message. 

Now, select the workflow that failed to run, then seelect Re-run all jobs. Therefore, the build should complete successfully.

What you will see is that a new branch named gh-pages got create. This will be branch specified when configuring github pages as described further below.

### Enable GitHub Pages

To enable GitHub pages on your GitHub repository, navigate to your respository, and select the **Settings**. From the left navigation menu, select **Pages**.

On the **GitHub Pages** settings page that appears, configure the following:

```
Source: Deploy from Branch
Branch: gh-pages
```

Then select save. After some time (waited for a minute or so on my end), refresh the page, and you should then be able to see the link to your static website

![GitHub pages link](/img/create_website_blog/7_github_pages_link.png)

With that, you have deployed an 11ty website hosted on GitHub pages. You site would look similar to the one shown below.

![11ty site](/img/create_website_blog/8_11ty_site.png)

### References

[1] “Permission denied to github-actions[bot]. The requested URL returned error: 403,” Stack Overflow. https://stackoverflow.com/questions/73687176/permission-denied-to-github-actionsbot-the-requested-url-returned-error-403 (accessed Sep. 05, 2023).