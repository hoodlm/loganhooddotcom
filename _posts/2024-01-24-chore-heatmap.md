---
layout: post
title: "The chore heatmap"
excerpt: "A spreadsheet-based tool for keeping track of recurring tasks"
imgpath: "/assets/img/posts/chore-heatmap"
---

The Chore Heatmap is a spreadsheet template I use for keeping track of recurring tasks like
household chores. You can download it [here]({{ page.imgpath }}/chore-heatmap.ods) if you want to try it out.

## Basic usage

|(A) Job Name       | (B) Frequency | (C) Last done  | (D) Next due date | (E) Days remaining |
|---------------------------------------------------------------------|
|Vacuum bedroom |7         |2024-01-23 |2024-01-30    |6              |
|Vacuum kitchen |7         |2024-01-20 |2024-01-27    |3              |
|Wash windows   |14        |2024-01-10 |2024-01-24    |0              |
|Purge fridge   |14        |2024-01-17 |2024-01-31    |7              |

List out the jobs that have to be done, and set a target frequency (in days) for each task.
For example, every room needs to be vacuumed once a week,
but the windows and the fridge only need to be cleaned every two weeks.

I populate the Last Done column by-hand, with the date that I most recently completed the chore.

The Next Due Date and Days Remaining columns are populated by formulas. For example, I vacuumed the bedroom
on January 23rd. Since the frequency is 7 days, the formula counts forward 7 days from January 23rd.

Days remaining is a countdown; I subtract today's date from the value calculated in column D.


|(A) Job Name       | (B) Frequency | (C) Last done  | (D) Next due date | (E) Days remaining |
|---------------------------------------------------------------------|
|Vacuum bedroom |7         |2024-01-23 |`=C1+B1`    |`=D1-TODAY()`  |
|Vacuum kitchen |7         |2024-01-20 |`=C2+B2`    |`=D2-TODAY()`  |
|Wash windows   |14        |2024-01-10 |`=C3+B3`    |`=D3-TODAY()`  |
|Purge fridge   |14        |2024-01-17 |`=C4+B4`    |`=D4-TODAY()`  |

That way, when I update column C, the Next Due Date and Days Remaining formulas are re-evaluated:

|(A) Job Name       | (B) Frequency | (C) Last done  | (D) Next due date | (E) Days remaining |
|---------------------------------------------------------------------|
|... |         | |    |             |
|Wash windows   |14        |_2024-01-24_ |**2024-02-07**{: class="slight-highlight"}    |**14**{: class="slight-highlight"} |
|... |         | |    |             |

The goal is, of course, to complete tasks on or before the Next Due Date.
But, when I fall behind and a task is past-due, then the Days Remaining column yields a negative number:

|(A) Job Name       | (B) Frequency | (C) Last done  | (D) Next due date | (E) Days remaining |
|---------------------------------------------------------------------|
|... |         | |    |             |
|Wash windows   |14        |2024-01-07 |2024-01-21 |**-3** |
|... |         | |    |             |

### Prioritization...

Suppose I've fallen behind on my kitchen cleaning duties, and I have both of these tasks overdue:

|(A) Job Name       | (B) Frequency | (C) Last done  | (D) Next due date | (E) Days remaining |
|---------------------------------------------------------------------|
|Vacuum kitchen |7         |2024-01-15 |2024-01-22    |-2             |
|Purge fridge   |14        |2024-01-08 |2024-01-22    |-2             |

Is it higher priority that I vacuum the floor or clean the fridge? Quantitatively, they're both
two days overdue (-2 days remaining) - but a different way of looking at this is that vacuuming
is 2/7ths of a cycle overdue, while cleaning the fridge is only 1/7th (2/14) of a cycle overdue.

So the template actually has a sixth column, Priority, dividing Days Remaining by Frequency.
I also multiply by -1, so higher numbers mean higher priority. The formula is `=-E/B`.

|(A) Job Name       | (B) Frequency | (C) Last done  | (D) Next due date | (E) Days remaining | (F) Priority
|---------------------------------------------------------------------|
|Vacuum kitchen |7         |2024-01-15 |2024-01-22    |-2             |  0.2857 |
|Purge fridge   |14        |2024-01-08 |2024-01-22    |-2             |  0.1428 |

I like to use automatic color formatting to color-code the Priority column - thus the Heatmap. The darker green items are safe, while the red ones are the ones I need to pay attention to.

<table>
  <thead>
    <tr>
      <th>(A) Job Name</th>
      <th>(B) Frequency</th>
      <th>(C) Last done</th>
      <th>(D) Next due date</th>
      <th>(E) Days remaining</th>
      <th>(F) Priority</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Vacuum bedroom</td>
      <td>7</td>
      <td>2024-01-23</td>
      <td>2024-01-30</td>
      <td>6</td>
      <td style="background-color:#5EB91E">-0.8571</td>
    </tr>
    <tr>
      <td>Vacuum kitchen</td>
      <td>14</td>
      <td>2024-01-20</td>
      <td>2024-01-27</td>
      <td>3</td>
      <td style="background-color:#AEDC7A">-0.4286</td>
    </tr>
    <tr>
      <td>Wash windows</td>
      <td>14</td>
      <td>2024-01-01</td>
      <td>2024-01-15</td>
      <td>-9</td>
      <td style="background-color:#FFD9B0">0.6429</td>
    </tr>
    <tr>
      <td>Purge fridge</td>
      <td>14</td>
      <td>2024-12-01</td>
      <td>2024-12-15</td>
      <td>-40</td>
      <td style="background-color:#FF5429">2.8571</td>
    </tr>
  </tbody>
</table>
