---
layout: post
title: What are the most overrated films?
categories:
- movies
tags:
- API
- films
- ggplot2
- movies
- overrated
- R
- rotten&nbsp;tomatoes
- rstats
- rbloggers
status: publish
type: post
published: true
meta:
  geo_public: '0'
  _publicize_pending: '1'
author:
permalink: overrated-underrated-films
---
<p style="text-align:left;">"Overrated" and "underrated" are slippery terms to try to quantify. An interesting way of looking at this, I thought, would be to compare the reviews of film critics with those of Joe Public, reasoning that a film which is roundly-lauded by the Hollywood press but proved disappointing for the real audience would be "overrated" and <em>vice versa</em>.</p>
<p>To get some data for this I turned to the most prominent review aggregator: <a title="Rotten Tomatoes" href="http://www.rottentomatoes.com/" target="_blank">Rotten Tomatoes</a>. All this analysis was done in the R programming language, and full code to reproduce it will be attached at the end.</p>

<h2>Rotten Tomatoes API</h2>
<p><a title="Rotten Tomatoes API" href="http://developer.rottentomatoes.com/docs/read/JSON" target="_blank">This API</a> is nicely documented, easy to access and permissive with rate limits, as well as being <em>cripplingly</em> restrictive in what data is presents. Want a list of all films in the database? Nope. Most reviewed? Top rated? Highest box-office takings? Nope.</p>
<p>The related forum is full of what seem like simple requests that should be available through the API but aren't: <a title="Top 100 lists requests" href="http://developer.rottentomatoes.com/forum/read/157176" target="_blank">top 100 lists?</a> Search using <a title="multiple IDs" href="http://developer.rottentomatoes.com/forum/read/112962" target="_blank">mulitple IDs at once</a>? Get <a title="audience reviews" href="http://developer.rottentomatoes.com/forum/read/112070" target="_blank">audience reviews</a>? All are unanswered or not currently implemented.</p>
<p>So the starting point (a big list of films) is actually kinda hard to get at. The Rube Golbergian method I eventually used was this:</p>
<ol>
<li>Get the "Top Rentals" list of movie details (max: 50)</li>
<li>Search each one for "Similar films" (max: 5)</li>
<li>Get the unique film IDs from step 2 and iterate</li>
</ol>
<p>(<strong>N.B.</strong> This wasn't my idea but one from a post in the API forums, unfortunately didn't save the link.)</p>

<img class="imgright" src="{{ site.baseurl }}/img/rottentomatohits.png" alt="Films returned" width="300" height="300" />

<p>In theory this grows your set of films at a reasonable pace, but in reality the number of unique films being returned was significantly lower (<em>shown right</em>). I guess this was due to pulling in "<a title="Walled garden wikipedia" href="https://en.wikipedia.org/wiki/Wikipedia:Walled_garden" target="_blank">walled gardens</a>" to my dataset, e.g. if a Harry Potter film was hit, each further round would pull in the 5 other films as most similar.</p>

<h2>Results</h2>
<p>Here's an overview of the critic and audience scores I collected through the Rotten Tomatoes API, with some outliers labelled.</p>
<p><a href="http://benjaminlmoore.files.wordpress.com/2014/05/rt_plot.png"><img class="imgfull" src="{{ site.baseurl }}/img/rt_plot.png" alt="Most over- and underrated films" width="500" height="533" /></a></p>
<p>On the whole it should be noted that critics and audience agree most of the time, as shown by the Pearson correlation coefficient between the two scores (0.71 across &gt;1200 films).</p>

<a href="http://blm.io/movies"><img class="imgright" src="{{ site.baseurl }}/img/screen-shot-2014-05-08-at-02-11-47.png" alt="" width="187" height="176" /></a>

<h3>Update:</h3>
<p>I've put together an interactive version of the same plot <a href="http://blm.io/movies" target="_blank">here</a> using the <a title="rCharts" href="https://github.com/ramnathv/rCharts" target="_blank">rCharts</a> R package. It'll show film title and review scores when you hover over a point so you know what you're looking at. Also I've more than doubled the size of the film dataset by repeating the above method for a couple more iterations — <a title="rCharts" href="http://blm.io/movies" target="_blank">take a look</a>!</p>

<h2>Most underrated films</h2>
<p>Using our earlier definition it's easy to build a table of those films where the audience ending up really liking a film that was panned by critics.</p>

<a href="http://benjaminlmoore.files.wordpress.com/2014/05/under_table.png"><img class="imgfull" src="{{ site.baseurl }}/img/under_table.png" alt="Scores are shown out of 100 for both aggregated critics and members of Rotten Tomatoes." width="500" height="366" /></a>

<p>Somewhat surprisingly, the top of the table is <a title="Facing the Giants" href="http://www.rottentomatoes.com/m/facing_the_giants/" target="_blank">Facing the Giants (2006)</a>, an evangelical Christian film. I guess non-Christians might have stayed away, and presumably it struck a chord within its target demographic — but after watching <a title="Facing the Giants" href="https://www.youtube.com/watch?v=4xneiV7Ru6Q" target="_blank">the trailer</a>, I'd probably agree with the critics on this one.</p>
<p>This showed that some weighting of the difference might be needed, at the very least weighting by number of reviews, but the Rotten Tomatoes API doesn't provide that data.</p>
<p>In addition the Rotten Tomatoes <a href="http://www.rottentomatoes.com/m/facing_the_giants/" target="_blank">page</a> for the film, shows a "want to see" percentage, rather than an audience score. This came up a few times and I've seen no explanation for it, presumably "want to see" rating is for unreleased films, but the API returns a separate (and undisclosed?) audience score for these films also.</p>

<a href="http://benjaminlmoore.files.wordpress.com/2014/05/rt_films.png"><img class="imgfull" src="{{ site.baseurl }}/img/rt_films.png" alt="Above shows a &quot;want to see&quot; rating, different to the &quot;liked it&quot; rating returned by the API and shown below" width="500" height="355" /></a>

Above shows a "want to see" rating, different to the "liked it" rating returned by the API and shown below. Note: these screenshots from RottenTomatoes.com are not CC licensed and is shown here under a claim of Fair Use, reproduced for comment/criticism.

<p>Looking over the rest of the table, it seems the public is more fond of gross-out or slapstick comedies (such as <a href="http://www.rottentomatoes.com/m/diary_of_a_mad_black_woman/" target="_blank">Diary of a Mad Black Woman (2005)</a>, <a href="http://www.rottentomatoes.com/m/grandmas_boy/" target="_blank">Grandma's boy (2006)</a>) than the critics. Again, not films I'd jump to defend as underrated. Bad Boys II however...</p>

<h2>Most overrated films</h2>
<p>Here we're looking at those films which the critics loved, but paying audiences were then less enthused.</p>
<p><a href="http://benjaminlmoore.files.wordpress.com/2014/05/over_table.png"><img class="imgfull" src="{{ site.baseurl }}/img/over_table.png" alt="As before, scores are out of 100 and they're ranked by difference between audience and critic scores." width="500" height="400" /></a></p>


<p>Strangely the top 15 (by difference) contains both the original 2001 <a title="Spy Kids" href="http://www.rottentomatoes.com/m/spy_kids/" target="_blank">Spy Kids</a> and the sequel <a title="Spy Kids 2" href="http://www.rottentomatoes.com/m/spy_kids_2_island_of_lost_dreams/" target="_blank">Spy Kids 2</a>: The Island of Lost Dreams (2002). What did critics see in these films that the public didn't? A possibility is bias in the audience reviews collected, the target audience is young children for these films and they probably are underrepresented amongst Rotten Tomatoes reviewers. Maybe there's even an enrichment for disgruntled parent chaperones.</p>
<p>Thankfully, though, in this table there's the type of film we might more associate with being "overrated" by critics. <a title="Momma's Man" href="http://www.rottentomatoes.com/m/10009419-mommas_man/" target="_blank">Momma's Man</a> (2008) is an indie drama debuted at the 26th Torino Film Festival. <a title="Essential Killing" href="http://www.rottentomatoes.com/m/essential_killing/" target="_blank">Essential Killing</a> is a 2010 drama and political thriller from Polish director and screenwriter <span style="color:#333333;">Jerzy Skolimowski. </span></p>
<p>There's also a smattering of Rom-Coms (<a title="Friends with Money" href="http://www.rottentomatoes.com/m/friends_with_money/" target="_blank">Friends with Money</a> (2006), <a title="Splash" href="http://www.rottentomatoes.com/m/1019641-splash/" target="_blank">Splash</a> (1984)) — if the API returned genre information it would be interesting to look for overall trends but, alas. Additional interesting variables to consider might be budget, the lead, reviews of producer's previous films... There's a lot of scope for interesting analysis here but it's currently just not possible with the Rotten Tomatoes API.</p>
<h3> Caveats / Extensions</h3>
<p>The full code will be posted below, so if you want to do a better job with this analysis, please do so and send me a link! :)</p>
<ul>
<li>Difference is too simple a metric. A better measure might be weighted by (e.g.) critic ranking. A film critics give 95% but audiences 75% might be more interesting than the same points difference between a 60/40 rated film.</li>
<li>There's something akin to a "founder effect" of my initial chosen films that makes it had to diversify the dataset, especially to films from previous decades and classics.</li>
<li>The Rotten Tomatoes API provides an IMDB id for cross-referencing, maybe that's a path to getting more data and building a better film list.</li>
</ul>
<hr />
<p style="text-align:right; font-size: .85rem;">Full code to reproduce analysis is available in <a href="https://gist.github.com/blmoore/dc3bfa9a3ad0857ac796" target="_blank">gist</a> form. <br />
This post was originally published on my <a href="http://benjaminlmoore.wordpress.com/2014/05/05/what-are-the-most-overrated-films/" target="_blank">Wordpress blog</a>.</p>
