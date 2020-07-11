
<figure>

<img src="/assets/images/r_logo_final.jpg" alt="drawing" width="200">

</figure>

### TL;DR

I wanted something that goes some way to automating my own
time-consuming process of scrolling twitter for cool things to read. I
thought a few other people might feel somewhat similar, so I decided to
build a feed.

[*R posts you might have missed*](https://twitter.com/icymi_r) is an
semi-automated twitter account posting recent R-related content. The
goal is to make it easier to keep up with the most important packages
and news from the community. Links to relevant and popular resources are
gathered from twitter and the R blogosphere before being processed and
lightly curated.

Read on to learn the origin story of the account, how it works and what
comes next\!

### News overload\!

<figure>

<img src="/assets/images/information_overload.gif">

</figure>

Keeping track of new developments in the data science, open source and R
communities is hard. The number of active developers, application areas
and R packages is exploding. Ever since I started writing R code I’ve
found it hard to avoid reinventing solutions to problems that are
already solved by other developers, usually through ignorance of those
developments. Being up-to-date with recent developments equips you with
options that can change the way you approach a new problem.

This is more or less the reason I still use twitter, because it’s still
the place where a majority of R developers hang out and share their
projects and ideas. The problem is that the volume of new stuff is just
too large - and I could easily spend endless hours per week scrolling
twitter, discovering and re-discovering new stuff (and getting very
distracted in the process). This is compounded by twitter’s news feed
algorithm which I think has made it even harder to develop a tailored
feed. So what can you do?

Well you’ve got options of course. [R
Bloggers](https://www.r-bloggers.com/) has been around for some time and
aggregates the feeds of several hundred well-known R blogs. I’ve never
found this solves my problem: blog articles are one type of content, but
there are many other types of content that I’d like to see in the same
place, and most of them do not have RSS feeds. The site itself carries a
lot of banner ads and doesn’t render articles very nicely - although
those may be minor considerations if you still use an RSS reader to
access the posts.

Ok so what else? [R-Weekly](https://rweekly.org/) is a terrific
resource. The team gather links to posts, packages, community news and
tweets into a single weekly digest. I think R Weekly is a wonderful
resource, and I still read it every week - it does a particularly good
job of creating a nicely formatted list broken into content types and
topics that were active in the last week. However, this doesn’t scratch
my itch completely. One issue is that it’s not totally automated (AFAIK
- please correct me if that’s false), and there’s always the risk that
something gets excluded. Additionally - any news oriented resource
focusses on what’s occurred most recently (of course, yeah I know) and
by definition excludes older useful resources that keep resurfacing. I
think it’s good for those things to continue to get air-time -
particularly because if I’m not working on a specific topic at the time
of the initial news announcement, I’ll probably forget about it. Or more
likely I just missed the announcement to begin with. I think repeated
exposure and reminders can be important.

Long story short, I wanted something that goes some way to automating my
own time-consuming process of scrolling twitter for cool things to read.
I thought a few other people might feel somewhat similar, so I decided
to build a feed.

### R posts you might have missed

*R posts you might have missed* is a twitter feed with the following
attributes:

  - Publishes about 10 posts per day
  - Posts are usually blog posts, repos and tutorials containing R code
  - Emphasis on non-commercial content that is free to access
  - Lightly curated with a lean towards more recent posts and repos
  - Ensure the author is directly credited in each post

### How does it work?

The recipe underpinning the feed takes the following steps:

**1. Gather links from \#rstats twitter**

  - Use Michael Kearney’s [`rtweet`](https://github.com/ropensci/rtweet)
    package to gather recent \#rstats tagged tweets from twitter (last 9
    days)
  - Also use [`rtweet`](https://github.com/ropensci/rtweet) to gather
    tweets from a subset of highly active users - not all of these are
    necessarily \#rstats tagged
  - Extract the urls embedded inside the tweets

**2. Gather new post urls from RSS feeds**

  - Use Robert Myles McDonnell’s
    [`tidyRSS`](https://github.com/RobertMyles/tidyRSS) package to read
    a [large number of RSS
    feeds](https://github.com/alastairrushworth/rssfeeds).  
  - Extract the urls of posts published in the last week that include
    code chunks

**3. Read and filter urls based on content**

  - Steps 1. and 2. usually result in around 2000 urls per week. Use
    [`htmldf`](https://github.com/alastairrushworth/htmldf) to download
    page content from the urls.
  - Filter out any pages that don’t have code tags in the source and
    that haven’t already been tweeted by *R posts you might have missed*
    recently.  
  - Filter out any commercial content, anything that looks spammy. This
    uses some simple lists of sites to exclude. Medium posts are also
    completely filtered out - Medium paywalls it’s content, and also
    tends to have lower quality content in general.
  - Extract page titles from `<meta>`, `<title>` or `<h1>` tags. For
    github repos, extract repo descriptions. These are used for
    composing tweets.
  - After reading and filtering, we’re usually down to about 300
    possible resources and urls we could tweet.
  - For each of the 300 pages, extract image urls on each page (images
    are selected manually in the next step). Download and convert any
    images that are base64 or SVG encoded to png (twitter doesn’t accept
    these file types in tweets).

**4. Find the author’s twitter username**

  - Usually, bloggers declare their social media profile information on
    their blogs. If this is the case, `htmldf` does a reasonable job of
    finding these automatically in the html.  
  - Author credentials are a bit trickier for github repos. Sometimes,
    this is directly embedded on the user’s GH profile - so all we need
    to do is visit the profile associated with the repo, and fetch the
    credentials from there. Sometimes twitter credentials aren’t
    provided here, but a personal website is declared on the GH profile
    where twitter profiles can be found. 80% of the time it seems that
    about 80% of R users twitter details can be gathered this way from
    their GH profile.

![/assets/images/gh\_profile.png](/assets/images/gh_profile.png)

**5. Compose tweets using an interactive shiny app**

Everything until this point is totally automatic and carried out using a
batch process on a cheap Google VM. Now the tweets are composed from
various ingredients that have been gathered. To do this, a simple GUI
built using R shiny, provides a simple editing environment to choose the
correct author credentials, choose an image to show with the post and to
check for any errors or formatting issues. For each tweet:

  - Check the authorship from a list of options gathered in the previous
    scraping steps.
  - Check the title, check emoji and choose a display image.
  - Filter out tweets that aren’t relevant.
  - Save the tweets to `.csv`: this includes columns for scheduled time
    (a randomly generated time in the week following `Sys.time()`),
    tweet text and image url.

![/assets/images/app\_gif.gif](/assets/images/app_gif.gif)

**6. Post\!**

  - Bulk upload the processed tweets to a scheduling service - I use
    [OneUpApp](oneupapp.io) who are particularly flexible with bulk
    uploads and cross-posting to other social networks.

![/assets/images/oneupapp.png](/assets/images/oneupapp.png)

### What next?

There’s a lot to do. In the short-term the intention is to

  - Reduce the effort involved in manual curation. The curation process
    takes about an hour for a week’s worth of R tweets, most of that
    time is checking author credentials are correct and that the urls
    contain high-quality content. A bit more NLP could help with both of
    these tasks.
  - Improve cross-posted author tagging for LinkedIn and Facebook posts.
    At present, full user credentials only appear on the twitter posts.
    It doesn’t seem to be possible/easy to schedule posts to LinkedIn
    with profile tags, where the author’s LinkedIn profile is known.  
  - Incorporate R-adjacent content. All of the candidate posts either
    contain code tags in the html, or are github repositories. Posts
    that are *about* R and data science but don’t include any code (like
    this one) are automatically excluded. It would be a big step to
    automatically identify and include these pages too.

### Are you a data scientist with an interest in Python?

[We’ve got you covered\!](https://twitter.com/icymi_py)

### Comments? Suggestions? Issues?

Feedback is very welcome\! Do you find *R posts you might have missed*
useful? What do you like? How would you improve it? Find me on twitter
at [rushworth\_a](https://twitter.com/rushworth_a) or [write a github
issue here](https://github.com/alastairrushworth/rpy_posts/issues).
