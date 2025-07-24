# On US income taxes

Last updated: 2025-07-24

## Why am I writing this?

The Tax Cuts and Jobs Act of 2017 during the first Trump administration
(henceforth, referred to as the TCJA) had a number of effects on the
personal income tax code.

This might seem like a pointless post to write, seeing as on paper, the
TCJA's changes to the individual tax code are slated to expire at the end
of next year, _but_ I would be rather surprised if Congress does not
prioritize renewing those provisions before then.

**July 2025 update: many of these provisions have indeed been renewed, at
least for now. There are very few changes in practice for
moderately-highly-compensated salaried employees. Chief among them is the
increase to the SALT cap to \$40,000, phasing out between \$500,000 and
\$600,000. Interestingly, since this now exceeds the standard deduction for
both individuals and married couples, this introduces a new marriage
penalty in certain situations.**

One might also think that everyone already knows all of the relevant
details by now, but I had a recent conversation that suggested
otherwise.

I'm only going to cover the implications that I've experienced personally,
so you'll be disappointed if you wanted to read about how stock options
interact with the Alternative Minimum Tax. The main points I want to cover
here are the points that arose in that discussion: (1) the marriage
penalty, and (2) RSUs.

Obligatory disclaimer: I am not a financial advisor (and even if I were, I
am not _your_ financial advisor). This post should not be construed as
financial advice.

## Marriage bonus vs penalty

Colloquially, the term "marriage bonus" refers to the situation where by
filing jointly, your tax obligations are reduced compared to filing
separately as single filers[^filing_separately]. Similarly, the term
"marriage penalty" refers to the situation where your tax obligations are
increased. I am not aware of states whose tax codes generate analogous
asymmetries, but it's not impossible.

As a result of the TCJA[^pre_tcja_penalty], the marriage penalty does not
exist for federal income tax if your joint income does not fall in the
highest bracket (for 2024, this will be \$751,601), as the joint filer
bracket cutoffs are exactly double the cutoffs for single filers for all
lower brackets.

Further, the marriage bonus does not exist for federal income tax if both
individuals' incomes fall in the same bracket.

Therefore, a simple rule of thumb is that if you have different marginal
tax rates, and your joint income does not push you into the highest tax
bracket for joint filers, then you should see a marriage bonus. If you have
the same marginal tax rate and do not fall within the highest tax bracket
for joint filers, then there will be no impact as far as individual taxes
are concerned.

**July 2025 update: this is not quite correct. Filing separately may allow
one or both individuals to claim the full SALT deduction, which for now has
a high enough cap that it exceeds the standard deduction. Technically, it
never captured all of the nuances, as there are situations where one spouse
would have enough deductions to itemize, and the other spouse could either
take the standard deduction, or file separately while also itemizing.**

There are two further points that come to mind as I write this.

First, filing jointly remains advantageous past the highest bracket. One
trivial example is the case of a single working spouse, as the joint
brackets are all higher than the individual brackets; this exacl at
situation continues to apply as long as the spouse with the lower income
makes no more than \$125,000 in 2024.

The phase-out gets rather complex, so I'd recommend plugging in numbers
into a calculator if you don't fit within one of the simple cases I
outlined above, but I would not expect the marriage penalty to apply as
long as the spouse with the lower income makes less than \$200,000.

Second, there are other differences between filing jointly and filing
individually, so the tax brackets do not tell the full story. For instance,
if you normally itemize your deductions, the state and local tax (SALT)
deduction is capped at ~~\$10,000~~ \$40,000 per tax return[^salt], whether
filing as a single filer or jointly. Another example is that in
Massachusetts, we have a deduction for rent paid, which similarly does not
have different caps for individuals versus couples. Further, there are
income limits[^backdoor] for various deductions that may no longer apply
upon getting married, e.g. student loan interest[^student_loans].

## RSUs and supplemental income

Another consequence of the TCJA is that it set withholding for supplemental
income at 22%[^millionaires] [^bonus_withholding] by default (previously,
it was 25%). Especially for workers in tech who receive restricted stock
units (RSUs) which are classified as "bonus" income, this often represents
drastic underwithholding and can lead to a surprise tax bill when April
comes around, and often an additional penalty for underpayment of taxes if
it causes the taxpayer to no longer fall within safe
harbor[^safe_harbor]. I've heard from several coworkers who were slapped
with a 5-figure tax bill in the aftermath of the TCJA, and an IRS penalty
to add insult to injury.

Aside: the psychology of tax filing as I understand it is weird: people
like seeing big tax refunds and dislike needing to pay more money, even if
the prevailing wisdom is that tax refunds are akin to giving the government
an interest-free loan, and needing to pay taxes when filing (provided that
you fall within safe harbor) is akin to receiving an interest-free loan. As
such, this change from the TCJA was exactly wrong from that psychological
standpoint, even if it meant people received slightly more money in each
paycheck. And, importantly, owing more taxes when filing does not actually
say anything about how your overall tax burden has changed.

There are a few common workarounds that I'm aware of:

1. Make (at least) quarterly estimated payments to the IRS.

1. Play around with your W-4 withholdings.

1. Some companies and payroll systems allow individuals to fill out a form
   that authorizes the company to perform additional supplemental
   withholdings. This is the easiest option if available, since it's
   set-and-forget, although specifying the "correct" number can take some
   effort. I personally use either 9 or 10%. (I can't remember exactly
   which offhand, but it's been good enough to push me into safe harbor.)

Short version: just do something that gets you back into safe harbor.

<hr>

[^backdoor]: Another notable income limit applies to direct Roth IRA
    contributions, but that's more of a nuisance than anything, given the
    existence of the backdoor Roth strategy.

[^bonus_withholding]: There is nothing in the tax code that explicitly
	authorizes companies to withhold an arbitrary different number, even if
	it would result in a more accurate result. As such, they generally go
	with the safe option.

[^filing_separately]: The federal tax brackets for "Married, filing
	separately" are strictly worse than remaining unmarried and filing as
	single filers. That said, I do not recommend lying to the IRS by filing
	as single filers if you are not actually single.

[^millionaires]: If you receive more than \$1,000,000 in supplemental wages
	in a given year, then you're subject to a different, higher withholding
	rate, and this section does not apply to you.

[^pre_tcja_penalty]: Prior to the TCJA, the tax brackets actually started
	diverging much sooner. In 2017, the ceiling for the 25% bracket was
	\$91,900 for a single filer but only \$153,100 for joint filers. As such,
	the TCJA actually eliminated the marriage penalty for most filers.

[^safe_harbor]: The IRS underpayment penalty generally does not apply if
	you paid either 90% of your actual tax obligations or more than 100% of
	your obligation for the previous year.

[^salt]: The SALT cap was one of the more contentious points of the TCJA,
	especially since it generally has more impact on filers in states with
	higher income and property taxes, which tend to be more Democratic (but
	are also home to several more moderate Republican members of
	Congress). In practice, the tax bracket changes and the increase to the
	standard deduction are such that filers generally come out ahead. In
	the past, I've created graphs to explore when the TCJA's provisions
	actually would result in a higher overall tax burden, and my
	recollection is that it just didn't happen in low-tax states, and that
	the cutoff was in the very high six digits or possibly low seven digits
	in all other cases I explored. Maybe someday I'll try to recreate those
	graphs.

[^student_loans]: This is actually a weird example because the income limit
	for joint filers is slightly more than twice the limit for single
	filers, but with sufficiently asymmetric incomes, you're not going to
	be able to claim it anyways.
