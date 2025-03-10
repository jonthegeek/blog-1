---
slug: pkg-news
title: "Why and how maintain a NEWS file for your R package?"
authors:
  - Maëlle Salmon
date: "2020-05-08"
tags:
- package development
- news
- documentation
output: 
  html_document:
    keep_md: true
---




Your R package is doomed to evolve as you add new features and bug fixes.
There is some value in cultivating a NEWS file as a response to package transformation.
In this post, we shall go through why keeping track of changes, how to create and maintain a changelog, advocating for the NEWS.md format.

## Why cultivate a changelog?

You will most probably change stuff in your package.
Discussing package evolution is beyond the scope of this post, refer to e.g. [this chapter of the rOpenSci dev guide](https://devguide.ropensci.org/evolution.html).
This post is about _documenting_ changes.
Even with a very clear version control history[^gitflow], having a text version of changes can be useful:

* to you and other developers, when looking back;

* to contributors, if you acknowledge their user name;

* to users, when updating the package or wondering when something changed;

* to you and other communicators, when drafting content (blog post, tweet, email) about a release.

How to write such a changelog, in practice?
We shall discuss format in the next section.
As regards contents, beside referring to the changelogs you like, [the NEWs chapter of the tidyverse style guide](https://style.tidyverse.org/news.html) is a very useful read.

## Why write the changelog as NEWS.md?

There are several possible formats for maintaining an R package changelog, according to the documentation of `utils::news()`: inst/NEWS.Rd formatted like other Rd files, a Markdown NEWS.md, plain-text NEWS or inst/NEWS.
In all cases, it means users can access the changelog 

* From the package CRAN page (in the case of CRAN packages, of course). See [`commonmark` NEWS](https://cran.rstudio.com/web/packages/commonmark/NEWS), [`ouch` NEWS](https://cran.r-project.org/web/packages/ouch/news.html), [`rhub` NEWS](https://cran.r-project.org/web/packages/rhub/news/news.html). The differences in URL path, display, page title are due to differences in the way the different changelog formats are parsed and rendered.

* with `utils::news()`, either reading it all at once

```r 
news(package = "rhub")
```

```
                        Changes in version 1.1.1                        

Enhancements

  - cran_summary() now messages that we recommend to fix all NOTEs,
    WARNINGs and ERRORs before a CRAN submission when the check results
    aren't 0 NOTE, 0 WARNING, 0 ERROR.

  - cran_summary() now outputs informative messages when any of the
    builds of the group hasn't completed (yet, or at all).

Bug fixes

  - cran_summary() now works for packages whose R CMD Check result
    include no NOTE/WARNING/ERROR, and gives an informative error
    message when not all builds are completed yet.

  - cran_summary() now prints lines to screen without unwanted
    indentation.

                        Changes in version 1.1.0                        

New features

  - New local_check_linux() function to run an R-hub check on the local
    host's Docker. New local_check_linux_images() function to list R-hub
    Docker images.

  - New check_on_solaris() shortcut to check on Solaris X86, without
    building the PDF manual or the vignettes.

  - New get_check() function that works with check ids, or a check group
    id.

  - list_package_checks() and list_my_checks() now output a tibble, that
    is nicely formatted when printed to the screen.

  - The output of get_check(), check(), check_on_, check_for_cran(),
    etc. functions gained
    
      - an urls() method returning a data.frame with URLs to the html
        and text logs, as well as the artifacts, of the check(s);
      - a browse() method replacing the web() method for opening the
        URLs corresponding to a rhub_check object.

  - New cran_summary() method to print a summary for a group or set of
    checks.

Bug fixes

  - In printing methods the submitted time is now always correct thanks
    to explicitly specifying units for as.numeric.difftime (@jimhester,
    #94 and @schloerke, #135).

                        Changes in version 1.0.2                        

First public release.
```

or specifying a query

```r 
news(
  query = grepl("check_on_solaris", Text),
  package = "rhub"
  )
```

```
                        Changes in version 1.1.0                        

New features

  - New local_check_linux() function to run an R-hub check on the local
    host's Docker. New local_check_linux_images() function to list R-hub
    Docker images.

  - New check_on_solaris() shortcut to check on Solaris X86, without
    building the PDF manual or the vignettes.

  - New get_check() function that works with check ids, or a check group
    id.

  - list_package_checks() and list_my_checks() now output a tibble, that
    is nicely formatted when printed to the screen.

  - The output of get_check(), check(), check_on_, check_for_cran(),
    etc. functions gained
    
      - an urls() method returning a data.frame with URLs to the html
        and text logs, as well as the artifacts, of the check(s);
      - a browse() method replacing the web() method for opening the
        URLs corresponding to a rhub_check object.

  - New cran_summary() method to print a summary for a group or set of
    checks.
```

Some users [might get creative and actually write their own function for browsing changelogs](https://twitter.com/pjs_228/status/1251793013879320576).

What NEWS format is the most popular? The actually serious [CRAN package `ouch`](https://kingaa.github.io/ouch/) uses inst/NEWS.Rd, like [134 other packages at the time of writing](https://github.com/search?l=&o=desc&q=org%3Acran+path%3A%2Finst%2F+filename%3ANEWS.Rd&s=indexed&type=Code).
In the case of `ouch`, inst/NEWS.Rd is actually [created from a plain-text inst/NEWS using `R CMD Rdconv`](https://github.com/kingaa/ouch/blob/8a2f39b895f97b7c8e8677f4052c42bbf16055c4/Makefile#L53-L54).
As regards plain-text NEWS files like [the one in commonmark source](https://github.com/jeroen/commonmark/blob/ea3b8fc0204832f889c4b84dee8670bf87d7e6b5/NEWS)[^irony], when preparing this post I found [413 inst/NEWS](https://github.com/search?q=org%3Acran+path%3A%2Finst%2F+filename%3ANEWS&type=Code) and [1,055 NEWS](https://github.com/search?l=Text&q=org%3Acran+path%3A%2F+filename%3ANEWS&type=Code) in CRAN packages.
Last but not least, at the time of writing there were [1,174 packages with a NEWS.md file](https://github.com/search?l=Markdown&q=org%3Acran+path%3A%2F+filename%3ANEWS&type=Code)

### NEWS.md is a great format

Now, why do I think NEWS.md is the best format? _Of course you are free to disagree and to present your worflow in the comments! :innocent:_

* If you're used to R Markdown, then it makes sense to use Markdown in NEWS.md i.e. it will come naturally to you.

* If your website has a `pkgdown` website, [NEWS.md will be rendered as a nice changelog page](https://pkgdown.r-lib.org/articles/pkgdown.html#news-1). See e.g. [`rhub` changelog](https://r-hub.github.io/rhub/news/index.html), notice how [links to GitHub users and issues resolve](https://r-hub.github.io/rhub/news/index.html#bug-fixes-1) with [no effort made in the NEWS.md itself](https://github.com/r-hub/rhub/blob/master/NEWS.md#bug-fixes-1)

* There is an `usethis` shortcut for creating a NEWS.md: [`usethis::use_news_md()`](https://usethis.r-lib.org/reference/use_news_md.html); using `usethis` to increase version will [add the corresponding heading to NEWS.md](https://usethis.r-lib.org/reference/use_version.html); and `usethis` will help you create an [informative GitHub release](https://usethis.r-lib.org/reference/use_github_release.html). Also note the interesting [`newsmd` package](https://github.com/Dschaykib/newsmd), like a `desc` for NEWS, that might help you write your own helpers?

### Limits of NEWS.md as a format

There are two limitations to NEWS.md.

* One caveat is the `utils::news()` function. If the user uses that and doesn't have `commonmark`[^cm] and `xml2` installed, [it will fail](https://github.com/wch/r-source/blob/90976a511bc3739e6773ea8e3d88b944b85b9aee/src/library/tools/R/news.R#L68)[^commonmark] -- which is [documented](https://github.com/wch/r-source/blob/acd751d2a1902cf17434216cf2e9c941726678d3/src/library/utils/man/news.Rd#L49) but not especially pleasant.

* Another caveat, more specific to GitHub, is that NEWS.md locally doesn't render links to issues magically, unlike GitHub releases (and unlike `pkgdown`). That is why [Yihui Xie uses a placeholder inst/NEWS.Rd in e.g. `knitr`](https://github.com/yihui/knitr/issues/1841#issuecomment-621247442). Maybe a good idea for a function would be one to convert from NEWS.md to an informative inst/NEWS.Rd as feature-rich as `pkgdown` changelog?

```r 
news(package = "knitr")
```

```
                       Changes in version 999.999                       

    o   This NEWS file is only a placeholder. The version 999.999 does
	not really exist. Please read the NEWS on Github: <URL:
	https://github.com/yihui/knitr/releases>
```

### Limits to NEWS as a communication channel

Now, no matter as good your NEWS.md file is, you need to keep in mind that some (most?) users will never read it. :wink: 
How to be sure they are informed, in that case?

On the one hand, communicating widely about executed or planned releases in [blog posts for users](/2019/04/08/rhub-1.1.1/) and [for developers](https://www.tidyverse.org/blog/2020/04/dplyr-1-0-0-package-dev/) can be useful.
If your blog post RSS feed is not a part of R Weekly, you can submit such links via [a PR to the R Weekly repo](https://github.com/rweekly/rweekly.org#how-to-contribute-by-using-this-repo) ("Updated packages" category) or via [R Weekly webform](https://rweekly.org/submit).
Maybe you even have a mailing list for users?
And to come back to `pkgdown` websites, you can [tweak the changelog](https://pkgdown.r-lib.org/articles/pkgdown.html#news-1) to have it display one page per major version and related minor versions, and to have the navbar feature release blog posts.

On the other hand, you can add information about changes _inside_ the package manual!
For instance, in `tidyr` docs there are lifecycle badges and explanatory text: compare the page of [`tidyr::gather()` (retired)](https://tidyr.tidyverse.org/reference/gather.html) and [`tidyr::pivot_longer()` (maturing)](https://tidyr.tidyverse.org/reference/pivot_longer.html).

## How and when to update the changelog?

<!--html_preserve-->{{% tweet "1236738064850051074" %}}<!--/html_preserve-->

Now, in terms of workflow, you could

* update the changelog for each contribution[^gitflowbis];

* only update the changelog before releases, by looking at version control history and the issue tracker, potentially using something like [GitHub milestones](https://help.github.com/en/github/managing-your-work-on-github/about-milestones);

* use [`fledge`](https://github.com/krlmlr/fledge) that _"has been designed to streamline the process of versioning R packages on Git, with the functionality to automatically update NEWS.md and DESCRIPTION with relevant information from recent commit messages"_.

In all cases you'll probably want to polish the changelog before releases, as [e.g. `usethis` would remind you](https://github.com/r-lib/usethis/blob/582a3fa886c042fe6c91376a6e4332df09a3db2a/R/release.R#L68).

## Conclusion

In this post we made the case for maintaining a changelog for your package, and for doing it in a NEWS.md file.
We also explained the limitations of NEWS.md as a way to efficiently inform users of changes, since users might lose track or never read changelogs: communicating around releases and adding lifecycle badges in the package manual itself can help mitigate those limitations.
As a package _user_, how do you follow NEWS of packages?[^github]
Do you ever read changelogs?[^changelog]

[^gitflow]: A good way to achieve one is adopting [git flow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow). You can make many stupid commits in a branch, and then squash and merge to master! It can also be wise to learn about [rewriting history](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History).
[^gitflowbis]: That'd pair well with git flow and some sort of [PR automatic checks](/2020/04/29/maintenance/#continuous-integration)!
[^commonmark]: Interestingly, for _extracting URLs_ out of NEWS.md for URL checks, R uses [another approach involving Pandoc](https://github.com/wch/r-source/blob/ca9e1ab9d15c73669d2dbdf989f59fd288aa3b3a/src/library/tools/R/urltools.R#L271).
[^changelog]: Reading changelogs can be useful but also pleasant, e.g. when discovering the [package uses version nicknames](http://naniar.njtierney.com/news/index.html). :sparkles:
[^irony]: Yes, `util::news()` uses `commonmark` to parse Markdown NEWS files but `commonmark`'s own NEWS file is plain-text. :shrug: :grin:
[^github]: For packages developed on GitHub, and if you don't oppose using GitHub, [there are different levels of repository watching including being notified of releases only](https://help.github.com/en/github/managing-subscriptions-and-notifications-on-github/viewing-your-subscriptions#configuring-your-watch-settings-for-an-individual-repository).
[^cm]: `commonmark` is [awesome for parsing Markdown](https://ropensci.org/technotes/2018/09/05/commonmark/)! `roxygen2` uses it [to parse tags with Markdown markup ](https://roxygen2.r-lib.org/articles/rd-formatting.html).
