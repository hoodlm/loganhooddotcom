---
layout: post
title: "Your investment portfolio grows as a geometric series"
excerpt: "Compound interest is only part of the story. Treating your saving and spending habits as inputs to a geometric series is a powerful tool for finanical decision-making."
imgpath: "/assets/img/posts/geometric-series"
---

## Compound interest

This year, millions of high school math students will learn about compound growth. Take $1000, put it in a bank account where it earns 5% each year. Because that extra $50 stays in the account, and earns its own interest, you get a little *more* interest every year.

<pre class="math">
f(0) =   $1000                        => $1000.00
f(1) =   $1000 * 1.05                 => $1050.00
f(2) =  ($1000 * 1.05) * 1.05         => $1157.63
f(3) = (($1000 * 1.05) * 1.05) * 1.05 => $1276.28
</pre>

And, with a bit of algebra to collapse the repeated x1.05 multipliers into an exponent, you end up with this neat formula:

<pre class="math">
f(t) = 1000*(1.05)<sup>t</sup>
</pre>

You can pick any **t** - any year in the future - and compute your accumulated interest in a single calculation, without needing to tabulate any previous years.

<pre class="math">
f(15) = $1000*(1.05)<sup>15</sup>
      = $1000*2.078928
      = $2078.93
</pre>


This is where the math teacher, the wise adult, will point out the profound personal finance lesson on the chalkboard. Suppose you, 15 year-old student, work all summer and make $1000. If you take that $1000 and put it in a high-yield savings account that yields 5% annual interest, by the time you're 30 you'll have twice as much money!

That's right, if you just wait until you are literally twice as old as you are now, you'll double your money... yes, this is the power of compound interest...

## Save-and-compound

Here's a variant that may be more compelling to that 15 year old:

You have $1000 in-hand from your summer job: go ahead and blow $700 of it on an iPhone or whatever, but invest the other $300. But, after next summer, you need to add *another* $300. And so on, for the next 15 years, with each previously-invested $300 continuing to yield 5%. The math works out to an accumulation of over **$7000** over that same 15 year period: $4500 in principal, $2597 in interest.

The math is a little more complicated. You can think of each $300 installment as its own unit of compound interest. They all compound at the same rate, but each installment is compounding for a different duration. Here's the brute-force way to calculate it:

<table>
  <thead>
    <th>Year invested</th><th>Installment</th><th>Years invested</th><th>Formula</th><th>Principal+Interest</th>
  </thead>
  <tbody>
    <tr><td>2024</td> <td>$300.00</td> <td>15</td> <td>$300*1.05<sup>15</sup></td> <td>$623.68</td></tr>
    <tr><td>2025</td> <td>$300.00</td> <td>14</td> <td>$300*1.05<sup>14</sup></td> <td>$593.98</td></tr>
    <tr><td>2026</td> <td>$300.00</td> <td>13</td> <td>$300*1.05<sup>13</sup></td> <td>$565.69</td></tr>
    <tr><td>2027</td> <td>$300.00</td> <td>12</td> <td>$300*1.05<sup>12</sup></td> <td>$538.76</td></tr>
    <tr><td>2028</td> <td>$300.00</td> <td>11</td> <td>$300*1.05<sup>11</sup></td> <td>$513.10</td></tr>
    <tr><td>2029</td> <td>$300.00</td> <td>10</td> <td>$300*1.05<sup>10</sup></td> <td>$488.67</td></tr>
    <tr><td>2030</td> <td>$300.00</td> <td>9</td> <td>$300*1.05<sup>9</sup></td> <td>$465.40</td></tr>
    <tr><td>2031</td> <td>$300.00</td> <td>8</td> <td>$300*1.05<sup>8</sup></td> <td>$443.24</td></tr>
    <tr><td>2032</td> <td>$300.00</td> <td>7</td> <td>$300*1.05<sup>7</sup></td> <td>$422.13</td></tr>
    <tr><td>2033</td> <td>$300.00</td> <td>6</td> <td>$300*1.05<sup>6</sup></td> <td>$402.03</td></tr>
    <tr><td>2034</td> <td>$300.00</td> <td>5</td> <td>$300*1.05<sup>5</sup></td> <td>$382.88</td></tr>
    <tr><td>2035</td> <td>$300.00</td> <td>4</td> <td>$300*1.05<sup>4</sup></td> <td>$364.65</td></tr>
    <tr><td>2036</td> <td>$300.00</td> <td>3</td> <td>$300*1.05<sup>3</sup></td> <td>$347.29</td></tr>
    <tr><td>2037</td> <td>$300.00</td> <td>2</td> <td>$300*1.05<sup>2</sup></td> <td>$330.75</td></tr>
    <tr><td>2038</td> <td>$300.00</td> <td>1</td> <td>$300*1.05</td> <td>$315.00</td></tr>
    <tr><td>2039</td> <td>$300.00</td> <td>0</td> <td>$300*1 (no interest yet)</td> <td>$300.00</td></tr>
  </tbody>
</table>

Summing up the Principal+Interest column yields the total of **$7,097.25**.

## A closed-form solution?

If we generalize and replace $300 with a constant P, and 1.05 with a constant R, our formula (for 15 years) is:

<pre class="math">
PR<sup>15</sup> + PR<sup>14</sup> + PR<sup>13</sup> + PR<sup>12</sup> + PR<sup>11</sup> + PR<sup>10</sup> + PR<sup>9</sup> + PR<sup>8</sup> + PR<sup>7</sup> + PR<sup>6</sup> + PR<sup>5</sup> + PR<sup>4</sup> + PR<sup>3</sup> + PR<sup>2</sup> + PR + P
</pre>

Just add more terms if we need more years.

Surely there's a more efficient way to do this?

Yes! There is a mathematical technique to shorten this. This type of equation is a well-known mathematical form called a **geometric series**, with a [closed-form sum formula](https://en.wikipedia.org/wiki/Geometric_series#Derivation_of_sum_formulas):

<pre class="math">
F(t) = P * (1 - R<sup>t+1</sup>)/(1 - R)
</pre>

It's a little more complex than our plain compound interest formula. Let's try it out, with P=$300 and R=1.05:

<pre class="math">
F(t) = $300 * (1 - 1.05<sup>t+1</sup>)/(-0.05)

F(1)  = $300 * (1 - 1.05<sup>2</sup>)/(-0.05) = $615.00
F(2)  = $300 * (1 - 1.05<sup>3</sup>)/(-0.05) = $945.75
F(3)  = $300 * (1 - 1.05<sup>4</sup>)/(-0.05) = $1293.04
...
F(10) = $300 * (1 - 1.05<sup>11</sup>)/(-0.05) = $4262.04
F(15) = $300 * (1 - 1.05<sup>16</sup>)/(-0.05) = $7097.25
F(50) = $300 * (1 - 1.05<sup>51</sup>)/(-0.05) = $66244.62
</pre>

### Another example with bigger numbers

[The Coffee Machine that can Pay for a University Education](https://www.mrmoneymustache.com/2011/05/12/the-coffee-machine-that-can-pay-for-a-university-education/) is a case study of a hypothetical two-adult household, where each adult spends $7 on a 'fancy' coffee every day. Looking ahead to their newborn child's college costs, they have a frugality epiphany, and start using an at-home coffee machine to save money. The claim in the article is that if they invest their coffee-savings at 7% annual interest, they'll have $148,000 after 17.5 years, to pay for their kid to go to college.

When I tried the math, I actually got an even higher figure of $182,000:

<pre class="math">
1 coffee/adult/day * 2 adults * $7/coffee * 365 days/year = $5110.00/year
F(17.5) = $5110.00 * (1 - 1.07<sup>18.5</sup>)/(-0.07) = $182,224.73
</pre>

# Visualizations

### One-time $1000 vs once-a-year $300

Here's the difference between the one-time $1000 investment earning compound interest on its own, compared to the $300-a-year geometric series, over 30 years:

![]({{ page.imgpath }}/001.png)

### One-time $4000 vs once-a-year $300

For a better comparison of the shape of the curves, we can bump up the initial one-time investment (the dotted red line) to $4000, so the curves are closer. They intersect after about 22 years.

![]({{ page.imgpath }}/002.png)

### Return-on-investment comparison

And finally, another important comparison is to show the amount invested vs return-on-investment. Note that the dotted red line - a one-time payment of $4000 - is a horizontal line. Everything between the dotted red line and the solid red line is interest earned.

The blue dotted line - the yearly investments of $300 - slopes upward linearly over time. Because the initial investment is small, it takes sometime before the solid blue line - total value, including interest - pulls away from the value of the principal.

![]({{ page.imgpath }}/003.png)

# Compound interest isn't the whole story

In recent years there's been interest in incorporating personal finance or "financial literacy" as a basic education subject. Here are two homework problems for *you* to try out:

* Over 10 years, what's the cost of paying $25/month for a subscription streaming service, compared to investing an extra $25/month?
* You finish a major project and your boss gives you a choice between a $10,000 bonus or a $1/hour raise. What's the better deal?

Going back to our allegorical high school math class: the personal finance tie-in shouldn't end with the compound interest formula! Teaching someone **how to model saving and spending habits as inputs to a geometric series** gives them a powerful tool for financial decision-making.

