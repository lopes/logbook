+++
title = "Changing the Zola Theme"
date  = 2022-08-29
description = "Valuable tips on changing your Zola blog theme without breaking the site."

[taxonomies]
tags = ["zola", "git", "blogging"]
+++

I started this blog because [I believe in a decentralized web](https://lopes.id/why-keep-your-own-website/), but along with this decision, comes a lot of administrative questions I had to deal with, and one of them if the theme.  The theme tells much about a site, and the first theme for this one was specially crafted by me, [ZOLA.386](https://github.com/lopes/zola.386).  Since I'm very proud for writing this theme even 2 years later, my desire to change it was increasing and when my professional and personal lives calmed down, I resurrected this question and ended up changing the theme.

Unfortunately, changing a theme in Zola is not a simple task because each theme comes with so many specific configurations that when you migrate, you must drop the old ones and put the new ones, or your site will not even "compile".  In this post I will bring some steps that others, like me, can take to change the visual identity of his page.


## Preparation
Zola has a increasing [gallery of themes](https://www.getzola.org/themes/) (ZOLA.386 is proudly [listed there](https://www.getzola.org/themes/zola-386/)), but it's far from other similar tools considering the number of available themes.  I recommend choosing one that is both beautiful (in your opinion) and well written (allows using Zola features at its maximum as well as it comes with useful integrations, like Google Analytics and Disqus).  It's important to **read the documents** of the chosen theme and get to know its main and mandatory configuration variables because it'll save a lot of time in the next steps.

Having chosen the new theme, the very first thing I recommend to do is to update Zola to the latest version --at least the version supported by the chosen theme, because it avoids some problems when building the site.  After that, it's time to start getting your hands dirty.  I recommend cloning the site repository, (Git is highly recommended to keep track of everything) and creating a new branch to work, because the new theme installation will brake many things at first and this way, all changes will be restricted to that branch.  Then, to start changing, add the new theme as a submodule under `themes` folder.

```sh
git clone --recursive https://github.com/lopes/anchor
cd anchor
git checkout -b v2
git submodule add https://github.com/aaranxu/tale-zola themes/tale-zola
```


## Migrating
I recommend starting the mess (hehe) by the main configuration file of your site: `config.toml`.  You'll need to start cleaning up this file by setting the new theme and dropping the options specific to the previous theme.  Then, you'd have to start adding the new options (specific for the new theme) and changing the common variables to fit the new theme.  The main tip here is: To change and test, option by option.  Do not make lots of changes to test, because if one of them breaks the site, it'll be harder to find the problem.

After updating the main central file, you will need to update page by page and post by post, if your previous used some setting that will not be needed anymore or if the new theme requires a specific attribute.  You should check the markdown syntax too because you would like to change any element inside the text for better presentation.  One tip is to fix one page or group of pages, test, and then roll up the changes for the entire site.  Changing everything at once and letting tests for the end, may cause so many problems at once that would be hard to diagnose and eventually revert.

While updating posts and pages, you could find some problems related to the lack of support of the new theme to older routines.  For instance, if the old theme supported the `resize_image` macro and the new one does not support it, and if it's broadly used in your content, you will have to find a way to overcome that, like migrating to the way the new theme deals with that, forking the theme and implementing the routine by yourself, or removing those elements.

Again, testing/compiling at every little change may increase the burden of single changes but will grant you the trust on how each configuration option impacts your site, giving you more administrative power over your content.


## Netlify
If you use [Netlify](https://www.netlify.com/) to host the site, you'll need to update `netlify.toml`, especially if you updated the Zola version.  It's always good to check on Netlify admin page if everything's fine because eventually, some features will have to be updated manually, like the build image version.  Checking such things before opening the Pull-Request will increase the chances of passing the tests.


## Deploy
Having everything working, it is time to send the new code to the server, test, and publish.  Since we're working in a separate branch, the `push` command will specify that it is gonna be uploaded:

```sh
git add .
git commit -am “change message”
git push --set-upstream origin v2
```

In GitHub, you will notice the interface tells you that one branch has commits that the main branch does not have.  To merge it, open a Pull-Request for that and describe the things changed in the title and in the comments section.  It will trigger some automatic tests between GitHub and Netlify and if everything's OK, clicking on the `Details` button will lead you to a preview page in Netlify for your site.  It's almost what you have in your machine when you run `zola serve` command, but seeing that preview grants you that in the server everything's fine, like the Zola version.

When you're good with the tests and the PR documentation, merge the PR, and it'll send the branch data to the main branch, triggering the CD routines, and thus rebuilding your site in Netlify with the new content.


## New Life
After that, your site should be in the new version and everything should be fine --after all, you tested locally and remotely.  In my case, always have some minor things to do after such a big change, then I prefer to use the same branch to push commits to fix those things.  I delete the branch only after the new version is stable, although GitHub automatically asks for that right after the PR is merged.

New theme, new life!  Go ahead!
