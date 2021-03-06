---
layout: post
title: 'Slidify: Modern, simple presentations written in R Markdown'
categories:
- r
tags:
- html5
- markdown
- presentations
- R
- rstats
- slidify
- rbloggers
status: publish
type: post
published: true
meta:
  _publicize_pending: '1'
  switch_like_status: '1'
author:
permalink: slidify
---

<p>As a LaTeX fan I'm used to using Beamer for presentations, but the built-in <a href="http://www.hartwork.org/beamer-theme-matrix/" target="_blank">themes</a> are definitely starting to show their age --- and writing a custom <code>.sty</code> file looks like a nightmare --- so for a while I've been looking at trying out an HTML5 framework.</p>

<a href="http://lab.hakim.se/reveal-js/" target="_blank"><img class="imgright" alt="Reveal.js is a great looking HTML presentation framework from Hakim El Hattab." src="{{ site.baseurl }}/img/screen-shot-2014-02-24-at-19-55-11.png" width="150" height="102" /></a>

<p>The first nice option I noticed was <a href="http://lab.hakim.se/reveal-js/" target="_blank">reveal.js</a> which seems to find a solid balance between looking sleek and modern, but not generating a prezi-like rollercoaster of a talk. Another project I came across, <a href="http://bartaz.github.io/impress.js/" target="_blank">impress.js</a>, probably leans towards the latter, and needs a decent array of web-dev skills to really customise.</p>

<p>These are both nice solutions but require decent web development skills to take advantage of, else offer limited web UI front-ends. An ideal solution for me would be simple to write and look great from the outset, needing only minor CSS, Javascript and HTML tweaks to build a good-looking and functional slide deck.</p>

<h2>Slidify</h2>

<p>Enter <a href="http://slidify.org/" target="_blank">slidify</a> written by Ramnath Vaidyanathan (<a href="https://github.com/ramnathv" target="_blank">github</a>), a wrapper of several libraries which allows you to go from simple R Markdown to slick HTML presentations. The <a href="http://slidify.org/samples/intro/" target="_blank">introductory presentation</a> gives a nice overview of what's possible and how simple these slide decks can be to write.</p>

<p>As the author modestly points out, slidify really is a go-between to other R packages:</p>

<ul>
<li><code>knitr</code> (<a href="http://yihui.name/knitr/" target="_blank">link</a>) — (a replacement for Sweave) think IPython notebook for R and other languages</li>
<li><code>whisker</code> (<a href="https://github.com/edwindj/whisker" target="_blank">link</a>) — for making use of <a href="http://mustache.github.io/mustache.5.html" target="_blank">mustache</a> (geddit?) "logic-less templating", which reminds me a lot of MediaWiki markup templates with extended functionality</li>
<li>R Markdown — the markdown extension introduced by the <a href="http://www.rstudio.com/" target="_blank">RStudio</a> team</li>
</ul>

<p>Together with slidify these packages make writing and customising presentations a breeze, so install the library from github (using Hadley Wickham's <code>devtools</code>) per the instructions <a href="http://slidify.org/start.html" target="_blank">here</a>. It also comes with some great default themes, like Google's io2012 (my favourite) and <a href="http://imakewebthings.com/deck.js/" target="_blank">deck.js</a>. The video below shows how to get started authoring presentations much better than I could:</p>

<div style="margin-left:auto; margin-right: auto; width:480px;">
<iframe width="480" height="360" src="//www.youtube.com/embed/I95GOmLc7TA" frameborder="0" allowfullscreen></iframe></div>

<h2>Features</h2>

<p>There's a tonne of cool things slidify can do that I haven't even explored yet, but that look great. Of course, through knitr you can embed R code, including analysis and plot generation, in your presentation, bringing together reproducible analysis and neat presentation of your results. Even cooler, it plays nice with <a href="http://rcharts.io/" target="_blank">rCharts</a> — from the same author — allowing interactive charts to be embedded in presentations; oh and Shiny applications can be added too, according to <a href="http://slidify.github.io/dcmeetup/demos/interactive/" target="_blank">this</a>.</p>
<p>Slidify enables straightforward github publishing (just <code>author()</code>) and RStudio allows quick upload to <a href="http://rpubs.com/" target="_blank">RPubs</a>, both make it trivial to have an online and archived copy of your presentation.</p>

<h3>Convert to PDF</h3>

<p>A PDF is a nice security blanket to have if you're worried about unforeseeable display issues on presentation day — it's a format designed to be environment independent after all. With the io2012 theme, this can be done natively from chrome using the print dialogue, <em>however </em>I<em> </em>consistently found that for my presentation at least, the active slide you are viewing and and sometimes an adjacent slide is glitched in the PDF output.</p>

<a href="http://benjaminlmoore.files.wordpress.com/2014/02/screen-shot-2014-02-24-at-22-37-58.png"><img class="imgright" alt="Chrome print PDF reproducibly bugs out on the active slide each time." src="{{ site.baseurl }}/img/screen-shot-2014-02-24-at-22-37-58.png" width="240" height="118" /></a>
</p>

<p>The hacky fix for this was to go to the final slide of the talk, print all but the last slide to PDF, then go back to an earlier slide and only print to file that remaining last slide. Then <a href="http://support.apple.com/kb/ht4075" target="_blank">stitch these files together</a> in Preview (assuming OS X), or <code>Imagemagick</code> or whatever.</p>

<p>Other than this active-slide glitch the PDF conversion worked surprisingly well and the output is passable as a decent presentation, albeit without the finesse of the subtle default transitions.</p>

<h4>Update:</h4>

<p>On twitter Ramnath <a href="https://twitter.com/ramnath_vaidya/status/438466893931094016" target="_blank">points out</a> that this is a recent problem with the Chrome browser, and Safari or Firefox should be able to export to PDF without issue. A quick check with Safari confirms that's the case for my presentation.</p>

<h2>Issues</h2>

<p>There were a couple of things that either can't be done (without digging deep into the js) or at least things I couldn't figure out after some (non-extensive) googling.</p>

<h3>Image features</h3>

<p>First, making things appear sequentially (like PowerPoint bullet points) is achieved simply with:</p>

```r
---
## Slide title
> * First bullet point to appear
> * Second...
```

<p>But for an image to then appear in the same way seems to require a continuation of the <code>ul</code>, i.e. your image needs a bullet point (?). Maybe I'm wrong on this but when included without the bullet point, the image seems to then precede the other bulleted content.</p>
<p>Another issue was resizing and centering images. I made use of the code from <a href="http://stackoverflow.com/a/18640582/1274516" target="_blank">this answer</a> on SO to add the quick and dirty CSS / jQuery to auto-centre / reduce oversized images for the slideshow. For me it would be nice for this to be default behaviour, but I suppose for a web developer this is trivial everyday stuff:</p>

```r
<!-- Limit image width and height -->
<style type="text/css">
img {
  max-height: 560px;
  max-width: 964px;
}
</style></p>
<p><!-- Center image on slide -->
<script type="text/javascript" src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.7.min.js"></script>
<script type="text/javascript">
$(function() {
  $("p:has(img)").addClass('centered');
});
</script>
```

<h3>RStudio integration</h3>

<p>This caused some confusion for me, but RStudio actually has its own presentation framework and uses slightly different markdown syntax to create it. On reviewing the two, it doesn't seem as developed as Slidify yet, and the defaults aren't as polished as the io2012 deck. The confusing part is, somewhere between the packages <code>slidify</code> and <code>slidifyLibraries</code> a function overloads RStudio's <code>Knit HTML</code> button, faking seamless integration.</p>

<p>The result is that the IDE is a great place to write the presentation, but I can't help thinking, as was mentioned <a href="https://twitter.com/kohske/status/436355371498618880" target="_blank">on twitter recently</a>, that the slidify framework would make a nice alternative or replacement for RStudio's current offering.</p>

<h3>Customising the title slide</h3>

<p>One of the problems I had was editing the theme's title slide. Most of the presentation is amenable to CSS hacking but the title slide is stubbornly hardcoded. (Well, it's not of course but the file is buried in the library install.)</p>

<p>The way I got at this file was by changing the presentation mode (within the YAML frontmatter) to <code>selfcontained</code>, and run slidify, copying the libraries folder to the same directory as your <code>.Rmd</code> file. Then, for the io2012 deck, the title slide template is at (<a href="http://stackoverflow.com/questions/15251751/adding-an-image-to-title-slide-using-slidify" target="_blank">thanks</a> Ramnath):</p>

<p><code>libraries/frameworks/io2012/partials/titleslide.html</code></p>

<h2>Conclusion</h2>

<p>I found slidify to be a great package and I ended up with, what I consider to be, the cleanest and nicest-looking presentation I've made to date. Also I've learnt a bit of web-programming along the way! I expect I'll be switching from beamer to slidify for future talks too.</p>

<a href="http://benjaminlmoore.files.wordpress.com/2014/02/screen-shot-2014-02-24-at-22-20-531.png"><img class="imgfull" alt="The final(ish) presentation in all its glory. Link may not work for long." src="{{ site.baseurl }}/img/screen-shot-2014-02-24-at-22-20-531.png" width="500" height="318" /></a>
