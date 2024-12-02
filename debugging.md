# Debugging

Last updated: 2024-08-21

## Intro

Over the years, I've had coworkers tell me that I'm good at debugging, and
I should teach people how to do what I do. For a while, my description of
my debugging process was ["the Feynman
method"](https://wiki.c2.com/?FeynmanAlgorithm):

> 1. Write down the problem.
> 1. Think real hard.
> 1. Write down the solution.

This is not very helpful, but for the longest time I didn't think I was
doing anything special. And, there were quite a number of scenarios where
I'd just see a problem, think about why it could manifest, guess exactly
what the failure mode was, and find that I was correct surprisingly often.

I still remember several conversations my first manager had with me wherein
he told me that "\[my\] superpower is debugging", and that I should figure
out how to "democratize the process" as a way of scaling out those
abilities beyond what I personally had time to do. (Throughout a number of
performance review cycles, he emphasized that one of my core strengths was
my ability to dig into problems.) At the time, I interpreted that by
creating tools that provided answers to several of the questions I would
normally ask when, say, investigating a weird blip in the network. But that
inevitably fell short, since once you have the output from such a tool, you
still need to figure out what to do with it. How do you interpret that
data? What do you ask as follow-ups?

I also recall discussions around the same timeframe with a "network
analyst"[^analyst] who was trying to figure out how to build tools such
that users didn't need a "priest" to interpret their results.

## Correlation

One of the earliest skills I identified as being relevant is
correlation. There's nothing quite like skimming through page after page
full of graphs and seeing something that spikes exactly at the start time
of whatever incident you're chasing. Or hunting through router logs for
weird lines about hardware faults that you don't really understand, but
hey, you can just copy-paste those at a friendly network engineer who'll
confirm that "yup, that line looks suspicious."

Spurious correlations are, of course, very likely to trip you up, which is
why it's important to look at history to make sure that this thing doesn't
happen on a regular basis, even when your users aren't complaining. And
sometimes you might see something that looks like it started about the
right time, but the shape or the scale of the problem isn't anything close
to big enough to explain the issues you're seeing.

It's also important to think about causality when observing
correlations. Why is this signal related to the problem you're seeing?
Should it be a leading or lagging indicator? Does it have a common
underlying cause? The granularity of your monitoring system's timestamps
can end up mattering.

At Google, there are at least two major timeseries monitoring systems; one
of them would consistently seem to violate causality because of differences
in how the two systems computed their timestamps. One system essentially
truncated timestamps when aligned to the minute, whereas the other provided
a backwards-looking view from the moment of collection. As such, we'd often
see two signals that should be perfectly correlated consistently differ by
a minute, always in the same direction. Understanding those low-level
details (or at least reasoning about them, and convincing yourself you know
what's going on) helps a lot with explaining weirdness that you see when
comparing signals across data sources.

## Storytelling

My understanding of my process has evolved over time: I recently made the
observation that what I'm actually good at is storytelling: I see some
things, I tell stories about what I see. I'm often wrong at first, or at
least not totally correct. That's fine! What's important is that either
someone else tells me that I'm wrong, or that I find new information that
contradicts part of my story, and then I have to adapt my story for the
next retelling. In a way, it's somewhat akin to the scientific method: I
come up with a hypothesis, then look for evidence that could either falsify
or support that hypothesis.

Talking to other people can often be helpful to the process, in a number of
ways:

* They can stand in as a [rubber
  duck](https://en.wikipedia.org/wiki/Rubber_duck_debugging), where the act
  of putting together your thoughts into a cohesive story sometimes
  highlights some previously innocuous details that are actually
  fundamental to the problem at hand.

* They tell you that you're wrong, and why. That's new information, likely
  in areas outside of your domain of expertise.

* They ask questions about your story that you hadn't thought to ask. You
  now have more threads to unravel to help bring you closer to a full
  understanding of the problem.

One other key point with the process is knowing when to ask more
questions. Sometimes this is intuition-driven, but other times
back-of-the-envelope calculations are required to determine whether certain
claims are justified.

For instance: a coworker and I were looking at a postmortem and saw the
claim that a cacheserver was allocating 16 bytes in a tight loop,
hitting its 1 TB limit in 30 minutes, and OOMing. But, the numbers didn't
line up. Our estimates placed that rate of allocations causing an OOM every
2-3 days; further, the heap profiles indicated that there were about 200
million allocated objects occupying the bulk of that 1TB. Upon further
tracing through the code, we found that the bug had caused the server to
allocate objects containing entire 4KiB pages at a time, which fit a lot
better with our observations.

But how do you know to question that assertion in the first place? You
can't just question every assertion you find, since you likely don't know
enough to falsify most claims. How are we performing an allocation? Is this
directly on the heap? Are these allocations sequential? How much
prefetching can or does happen? My best answer is that sometimes, something
just feels off, and you just have to double-check that things make
sense. This again isn't a very satisfying answer, but my process is still
somewhat of a mystery to me at times.

<!-- TODO: ## Intuition -->

## Personality traits?

I've also talked with coworkers to try to figure out what makes for a
highly successful debugger -- why isn't everyone else as good at getting to
the bottom of these problems? Surely I'm not doing anything special?

One coworker in particular identified several others that we both agreed on
as fitting that description, pointed out that all of us came from very
different backgrounds, and highlighted what he thought were personality
traits that we had in common:

- Curiosity.
- A certain stubbornness (perhaps more politely: tenacity).

That's a pretty good combination: asking "wait, why does that work"
followed by refusing to give up until you get a sufficiently satisfactory
answer will go quite far.

I also came up with some other guesses at predictive (but not
determinative) factors:

- Having side projects, whether it's maintaining a Wordpress blog, running
  a Kubernetes cluster at home, or even periodically scraping brewery
  taplists to identify new releases.
  
  I posit that this effectively increases years of experience -- whether
  via toying around with new libraries, SSHing into VMs to fix broken
  pipelines, dealing with external API changes where asking for a rollback
  isn't an option, or just writing, debugging, refactoring code over the
  course of years.

- Coming from an engineering background, which I suspected might lead to
  nurturing those traits.
  
  I've certainly had to field a number of phone calls over the years from
  my dad who has questions about some random Python framework that he's
  been playing with. What's important isn't that I necessarily know the
  answers to all of his questions (I don't), but just getting exposure to
  that style of thinking where "You have a question about some natural
  phenomenon -- let's write a simulation to test our intuition!"

- Being a polyglot (at least in the programming language sense), out of
  necessity rather than chasing fads.
  
  I've gone in and edited C# projects to fix breakages due to API changes,
  despite never having written the equivalent of "hello world" in that
  language. I've crafted overly-complicated SQL queries (initially, with
  far too many levels of nesting) just to answer questions where no
  sensible person would think SQL would be the right answer (extracting
  header fields from pcaps? identifying geo-expansion targets by joining
  world population data with datacenter locations? etc). And I'm not afraid
  of fixing weird web UI rendering bugs involving JS / CSS.
  
  I've come to realize that this behavior is actually special -- I've
  encountered plenty of people who refuse to touch certain codebases
  because "I don't do UI" or "I don't know [insert framework here]" or "I
  don't use \[Windows/Mac/Linux\]." I mean, I didn't know what I was doing
  either at first, but I didn't let that stop me. It's not even "fake it
  until you make it" -- I made it very clear that I didn't know what I was
  doing, but exploration is a solid way to learn how things work. A
  coworker asked me "Steve, how do you know everything?" My response was
  "very fast code search[^codesearch]" and provided screenshots of my
  browser history to prove it.

## Conclusion

I still don't really know what makes someone good at debugging, but I think
I'm closer than I was a few years ago.

<hr>

[^analyst]: In his words: "My job title says that I'm a network analyst,
    whatever that means." As far as his job family was concerned, the last
    time I checked, about 4 years ago, he was the equivalent of a Staff
    Network Engineer.

[^codesearch]: Code Search at Google is one of the most useful tools at
    Google, and one of the most cited things that people miss when they
    move to a new company. As a search company, you'd think that Google
    would be better at building search into our products, and in most cases
    you'd be disappointed, but not with Code Search.
