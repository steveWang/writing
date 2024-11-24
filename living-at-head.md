# Living at Head in Practice, and Envoy

Last updated: 2024-11-24

Google famously has a monorepo, where to a first approximation[^silos],
anyone can read any of the code, write a proposed change, and send it for
review. This has a number of advantages: greatly reduced version skew,
enablement of large-scale refactoring, simpler cross-team collaboration,
improved internal mobility, and so on. [_Software Engineering at
Google_][swe_book_monorepo] argues a number of these points. Google
additionally imposes the notion of code owners who must approve any changes
within their projects.

Perhaps one of the salient consequences of a monorepo based on a version
control system with linear history (as is the case with Perforce) is that
it enables what Abseil refers to as ["living at head"][abseil_live_at_head]
If you accept all the arguments at face value, this sounds great. But I
posit that an organization is only able to truly live at head with its own
first-party libraries.

## Sources of truth

At Google, we have a number of projects that we've opted to open-source,
typically via standardized tooling such as [copybara]. Generally, when you
have multiple copies of a repository that may diverge, you need some way of
reconciling the two. The simplest approach is to just treat one as the
source of truth. Often, projects start out where the internal version is
authoritative, and the Github repository is treated as a mirror of the
internal library; as time progresses, that may flip such that all
development begins on Github.

Of course, it's rare for a well-supported library to adhere to one of these
two extremes. It's more common to have internal changes exported to Github,
and external changes imported back into the monorepo. After all, copybara
supports both. And, ideally we have frequent enough synchronization between
the two such that merge conflicts are rare.

## Case Study: Envoy

Envoy is an OSS project for an extensible proxy, originally from Lyft, but
with much Google engineering investment in the past 5 years. In recent
years, it's been quite trendy for providing a framework for service mesh
networking (Istio), among other things, but that's not really what I want
to talk about here.

Envoy is not a first-party Google project, and as such, it follows the
latter model where development occurs primarily on Github, and commits are
batched and imported into our monorepo on a somewhat regular
cadence. Because Google generally lives at head, this has contributed to a
breakdown of typical API abstractions, such that much internal usage of
Envoy treats it as a library, not a framework with a published and
supported API. We use the library headers directly, peeking into the
internals of how Envoy does things, and so when an internal refactor lands
upstream, it has the potential to break many, many projects internally
(_cf._ Hyrum's law).

As such, we have an internal rotation for the Envoy import process, since
it is so failure-prone that automated tooling often just does not
work. This introduces its own set of problems.

The import process increases latency between when a problem is fixed at
Github HEAD and when it's available internally, since the importer must
manually fix all the errors detected by continuous integration (CI), and
this process takes time. While the import process is nominally weekly, it's
not unheard of for the last successful import to be multiple weeks
out-of-date, or for certain commits to take more than a month to be
available internally. (There's additionally security review that occurs for
each commit, which provides yet another source of latency.)

The importer may be an expert in the use of Envoy but, even if so, usually
does not have full context regarding any particular Github commit nor the
individual uses of Envoy. As such, the primary role of the Envoy importer
is to "make the code compile," as the per-project familiarity is largely
offloaded to individual code owners during the review process. However, the
code owners do not necessarily know the full context of the Github commit,
and as such, bugs can easily slip in during the import process.

These bugs are even more likely to go unnoticed until later stages of
development (even potentially as late as production) if not covered by
tests in the CI pipeline (see: the [Beyoncé rule][beyonce]).

To further complicate matters, Envoy on Github does not live at head. One
of the more absurd consequences of this is that Envoy depends on a specific
snapshot of [QUICHE], which is exported from google3; for a long time, we
actually imported that specific snapshot of QUICHE back into google3,
thereby violating our one-version rule. If done naïvely, this would have
resulted in one-definition-rule violations and inscrutable compile-time and
runtime errors, so we then renamespaced all of the reimported quiche
symbols under `external_quiche_quic` and so forth; this naturally made it
impossible to reuse existing libraries built on top of QUICHE with Envoy
without introducing otherwise unnecessary tech debt.

Envoy not living at head has caused other problems with dependency version
skew, such as internal projects depending on features in the latest version
of, say, protobuf or gRPC, while not considering the upgrade path
externally. As such, those projects often take initiative to fork certain
parts of Envoy, thereby making it even more complicated to unify that logic
once the dependencies have been brought up-to-date.

## Case Study: Abseil

Abseil occasionally introduces breaking changes. When it does so, the
libraries team executes some large-scale refactors to get google3 off of
the deprecated API, and then removes it. This works fine for google3, but
it doesn't do anything for external users. Okay, well, Abseil promises to
provide a tool to refactor "well-behaved code" in the event of
backwards-incompatible API changes.

In practice, if you wish to use Abseil at head, then all of your
dependencies also need to use Abseil at head, or you have to be willing to
maintain patches for those dependencies for any breakages.

## Closing thoughts

Living at head sounds good on paper, but in practice, I haven't seen it
work for external libraries, especially when you don't fully own all of
your dependencies (at least up to a certain point; we certainly don't own
Clang or the Linux kernel, and indeed, in those cases we tend to pin to a
specific version). Perhaps we're doing something very wrong. Or perhaps
it's just an unsolvable problem.

[^silos]: Certain high-value and/or sensitive code is restricted to
    "silos". Some popular examples: security patches, much of Waymo code,
    the Google Search ranking details.

[abseil_live_at_head]: https://abseil.io/about/philosophy#we-recommend-that-you-choose-to-live-at-head

[beyonce]: https://abseil.io/resources/swe-book/html/ch11.html#the_beyonceacutesemicolon_rule

[copybara]: https://github.com/google/copybara

[QUICHE]: https://github.com/google/quiche

[swe_book_monorepo]: https://abseil.io/resources/swe-book/html/ch16.html#monorepos
