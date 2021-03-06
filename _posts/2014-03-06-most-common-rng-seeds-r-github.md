---
layout: post
title: What are the most common RNG seeds used in R scripts on Github?
categories:
- r
tags:
- BigQuery
- Github
- PRNG
- R
- RNG
- seed
- rbloggers
status: publish
type: post
published: true
meta:
  _publicize_pending: '1'
  _wp_old_slug: seeds-of-randomness-github-rng-analysis-in-r
author:
permalink: prng-seeds
---

<p>In the R programming language, the random number generator (RNG) is seeded each session using the current time and process ID. Via the magic of the popular <a href="https://en.wikipedia.org/wiki/Mersenne_twister" target="_blank">Mersenne Twister PRNG</a>, the values stored in <code>.Random.seed</code> are used sequentially each time "randomness" is invoked in a function. This means, of course, that the same function run in different R sessions can produce varying results, and in the case of modelling a system sensitive to initial conditions the observed differences could be huge. </p>

<p>For this reason it's common to manually set the PRNG seed (using <code>set.seed()</code> in R), thereby creating the same <code>.Random.seed</code> vector which can be drawn from in your analysis to produce reproducible results. The actual value passed to this function is irrelevant for practical purposes &mdash; for whatever reason I generally user the same number across projects (42) &mdash; so this made me wonder: what values do the major R developers tend to pick?</p>

<h2>To Github</h2>

<p>The <a href="http://developer.github.com/v3/" title="github API" target="_blank">Github API</a> is currently in a transitional period between versions 2 and 3 and has (annoyingly) limited code search results to specific users or organisations. This means to perform a code search programmatically, I'll need a list of R users.</p>

<h3>Google BigQuery</h3>

<p>One way of building a list is through <a href="http://www.githubarchive.org/" title="Github archive" target="_blank">Github archive</a>. The dev (<a href="http://www.igvita.com/" title="Ilya Grigorik" target="_blank">Ilya Grigorik</a>) has put up a public dataset with <a href="https://developers.google.com/bigquery/" title="BigQuery" target="_blank">Google BigQuery</a>, which is a neat cloud-based platform for querying huge datasets. My SQL isn't all that, but the Google BigQuery interface is really functional (e.g. it autocompletes table fields) and makes it easy to get the data you're looking for.</p>

<img src="{{ site.baseurl }}/img/screen-shot-2014-03-06-at-12-21-52.png" alt="Fishing for prolific R users via Google BigQuery." width="500" height="499" class="imgfull" />

<p>In this case I pulled out R code repositories ordered by repo pushes (as a heuristic for codebase size and activity, I guess) with their owner's username. It was this list of names I then used for the API query.</p>
<h3>Github API</h3>
<p>It looks like there's a decent <a href="https://github.com/cscheid/rgithub" title="R bindings" target="_blank">set of R bindings</a> for the Github API, but it's not clear how they work with code search, so I opted for the messier option of calling curl through <code>system()</code>. To build the command to search the API per user:</p>

```r
getCMD <- function(user){
  cmd <- paste0("curl -H 'Accept: application/vnd.github.v3.text-match+json Authorization: token ",
    oauth, "'     'https://api.github.com/search/code?q=set.seed+in:file+language:R+user:",
    user,"&amp;page=1&amp;per_page=500' | grep 'fragment' -")
  return(cmd)
}
```


<p>As you can see this is pretty rough and ready, there may be a pagination issue if someone sets PRNG seeds everywhere but it'll do.</p>
<p>You can then run the command and pull out the returned string matches for the query, in this case I searched for "set.seed" and then used Haldey Wickham's <code>stringr</code> <a href="http://cran.r-project.org/web/packages/stringr/index.html" title="stringr" target="_blank">R package</a> to regexp out the number (if any) passed to the function.</p>

```r
getPRNGseeds <- function(user){
  print(user)
  api.result <- system(getCMD(user), intern=T)
  if(is.null(attr(api.result, "status"))) {
    seeds <- cbind(user,
        str_match(api.result, "set\\.seed\\((\\d+)\\)")[,2])
    Sys.sleep(10)
    return(seeds)
  } else {
    cat(user, " failed")
    return(cbind(user, NA))
  }
}
```

<p>The Github API spits out JSON data (ignored and just grepped out in the above) so I looked into a couple of smarter ways of parsing it. Firstly there's the <code>jsonlite</code> <a href="https://github.com/jeroenooms/jsonlite#readme" title="jsonlite" target="_blank">R package</a> which offers the <code>fromJSON()</code> function to import JSON data into what resembles a sometimes-hard-to-work-with nested R object. It seems like the Github API query results return too many nested layers to produce a useful object in this case. Another option is <a href="http://stedolan.github.io/jq/" title="jq" target="_blank">jq</a>, a command-line program which has a neat syntax for dealing specifically with the JSON data structure &mdash; I'll definitely be using it for more complex JSON wrangling in the future.</p>

<h2>The data</h2>
<p>Despite the harsh search limitations I ended up with 27 users who owned the top 100 R repositories, and of those 15 used <code>set.seed()</code> somewhere (or at least something like it). However the regex fails in some cases &mdash; where a variable is being passed to the function instead of an integer, for one. Long story short, I scraped together 187 lines of <code>set.seed(\d+)</code> from some of the big names in the R community and here's how the counts looked:</p>
<p><a href="http://benjaminlmoore.files.wordpress.com/2014/03/prng.png"><img src="{{ site.baseurl }}/img/prng.png" alt="RNG seeds" width="500" height="833" class="imgfull" /></a></p>
<p>So plain old <strong>1</strong> is the stand-out winner! </p>
<p>There's a few sequences in there (<strong>123</strong>, <strong>321</strong>, <strong>1234</strong>, <strong>12345</strong>) and some date references (<strong>2011</strong>, <strong>20051028</strong>), but surprisingly few programmer in-jokes or web-culture references, save a lone <strong>1337</strong> and I guess some binary. </p>
<p><strong>1410</strong> (or <strong>1014</strong> in less-sensible countries) and <strong>141079</strong> look like they could be a certain R developer's birthday and birth year, but that's pure speculation :)</p>
<p>Here's one of those awful wordle / wordcloud things too.</p>
<p><a href="http://benjaminlmoore.files.wordpress.com/2014/03/wordle_crop.png"><img src="{{ site.baseurl }}/img/wordle_crop.png" alt="wordle_crop" width="300" height="287" class="imgfull" /></a></p>
<p>Hopefully as the roll-out of the v3 Github API progresses the current search restriction will be lifted, still this was a fun glimpse at other programmer's conventions!</p>
<hr />
<p style="text-align:right; font-size: .85rem;">Full code to reproduce in this <a href="https://gist.github.com/blmoore/9400832">gist</a>.<br />
This post was originally published on my
<a href="http://benjaminlmoore.wordpress.com/2014/03/06/most-common-rng-seeds-r-github/" target="_blank">Wordpress blog</a>.</p>
