---
layout: post
title: "Using EC2 Spot for the PrimeGrid Compositorial Prime Search"
excerpt: "The most cost effective way to find prime numbers on EC2!"
imgpath: "/assets/img/posts/primegrid-1"
---

In my [previous post](/2025/10/02/primegrid-1.html) I talked a bit about my recent interest in PrimeGrid.

Last month, the PrimeGrid team launched a new subproject to search for Compositorial Prime Numbers.
To drum up interest in the Compositorial Prime Search (CPS), they accompanied the launch of the
new sub-project with a multi-day "Challenge". PrimeGrid runs a few Challenges each year; they're a
few days where individuals and teams participate in friendly competition to complete lots of tasks
in a particular subproject.

## What's a Compositorial Prime Number?

The compositorials are a sequence of numbers created by multiplying composite (non-prime) numbers together:

```
24     = 4 * 6
192    = 4 * 6 * 8
1728   = 4 * 6 * 8 * 9
17280  = 4 * 6 * 8 * 9 * 10
207360 = 4 * 6 * 8 * 9 * 10 * 12
...
```

The conventional syntax for compositorials is as a ratio `N!/N#` - "N factorial" (all integers up to N) divided by "N primorial"
(all prime numbers up to N). For example 207360 can be written as `12!/12#`.

**Compositorial primes** are prime numbers that are +-1 from a Compositorial number. 23, 191, and 193 are examples of
compositorial primes. They're not very widely studied - yet. Before September 2025, no one knew very much about
their distribution or frequency.

Since this is a bit of bleeding-edge mathematics, I thought it might be fun to put a little more "muscle" into the
challenge; more than just my personal computers. This is the kind of thing The Cloud is for, right?

# EC2 Spot

**Disclaimer:** As of writing this post, I work for AWS. I think there's a clause in my
non-compete agreement that says that if I write or speak publicly about personal projects,
I should prefer my employer's products. But really it's mostly just that I'm
familiar with EC2. (Please don't tell my employer that I'm
[also running](https://www.primegrid.com/show_host_detail.php?hostid=1382005)
PrimeGrid on O\*\*\*\*\*'s very generous free tier.)
{: class="serious-security-business"}

With that out of the way: I actually have always wanted to do something with EC2 Spot. My
day-to-day work uses EC2 heavily, but mostly in the ivory tower of highly-available services
and predictable constant-work distribution pipelines; not the exciting wild west of
demand-driven variable pricing and interruptable batch processing.

How EC2 Spot works: AWS has a large catalog of specialized instance types and sizes. Sometimes,
there might be "too many" of some instance sizes, so AWS prices them at a discounted Spot Price, up to 90% off the regular price.
The catch, and why you get the discount, is that The Algorithm (that is, EC2's capacity planning systems)
may decide at any time that the slice-of-server that's running your EC2 Instance is better used for something else.
When this happens, you get a 2 minute warning, then you get booted off.

Are interruptions a major problem for long-running PrimeGrid jobs? Not necessarily. The programs checkpoint progress to disk
very frequently. EC2 Spot gives you the option to either *Terminate* instances or *Stop* them when they're interrupted.
Terminating is just what it sounds like: the computer and anything you saved is gone forever.
Stopping is basically like shutting down a computer; the contents of the disk (which in EC2 is network-attached storage,
called an EBS Volume) are still saved somewhere. Theorically, if The Algorithm decides that your instance can be restarted later,
then the PrimeGrid program can pick up where it left off. More later on the "theoretically" qualifier.

# Preparations

## Step 0: Get Money

There is nothing more virtuous than funding your hobbies through decluttering.

![selling a bedframe on $50 on Facebook marketplace]({{ page.imgpath }}/everydayimhustlin.jpg)

I also did a Neilson survey for $10, so that gives me a budget of $60.

## Step 1: Get BOINC running on EC2

For personal projects I prefer Ubuntu Server. (It's not necessarily the best, but it's what I know.)
When I launch an Ubuntu EC2 instance, it doesn't have a monitor and mouse and keyboard like a personal computer;
it's a "headless" server that I have to remotely manage with a command line.

Over SSH, I can follow [Boinc's standard Linux installation instructions](https://boinc.berkeley.edu/linux_install.php),
then run a couple commands to start it:

```
# (1) start boinc in the background
boinc &
# (2) attach to primegrid with my credentials:
boinccmd --host 127.0.0.1 --project_attach https://www.primegrid.com ${ACCOUNT_KEY}
# (3) watch the tasks come in!
watch -n3 boinccmd --host 127.0.0.1 --get_task_summary
```

This works as a proof-of-concept; in particular, it was good to quickly confirm that BOINC and Primegrid
don't block AWS's public IP ranges.

I don't want to set up instances manually, though. (This is a DEVOPS household!)

### Start-up script

EC2, like other cloud providers, allows me to specify a userdata script that is executed when an instance boots up.
This way, I can request my instance, and as soon as it launches, it will download the BOINC client and start running
tasks, without me needing to SSH and manually set up anything. (Remember, the Cloud bills you for every second your instance
is powered on, whether your CPU is idle or looking for prime numbers. I'm trying to get every bit of value out of my $60
budget, so every second counts!)

I used an old sysadmin trick of launching the processes in a background `tmux` session, so if I want to SSH for troubleshooting or
reconfiguration, I can attach interactively.

Here's the script:

```
#! /bin/bash
set -eu

export PATH="/usr/bin/:/usr/local/bin/"

### Install BOINC and tmux
curl -fsSL https://boinc.berkeley.edu/dl/linux/stable/noble/boinc.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/boinc.gpg
echo deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/boinc.gpg] \
    https://boinc.berkeley.edu/dl/linux/stable/noble noble main \
    | tee /etc/apt/sources.list.d/boinc.list > /dev/null
apt update
apt install -y boinc-client tmux

### Switch to a non-root user to launch BOINC
sudo -u ubuntu -i <<'EOF'
mkdir -pv /home/ubuntu/primegrid
cd /home/ubuntu/primegrid

### Start boinc + tasklist in tmux
# run the boinc client in one tmux pane
tmux new-session -s "primegrid" -d "boinc"
# poll task status in a lower split-pane
tmux split-window -t "primegrid" -d \
    "sleep 5; \
    boinccmd --host 127.0.0.1 --project_attach https://www.primegrid.com <ACCOUNTKEYHERE>; \
    watch -n10 boinccmd --host 127.0.0.1 --get_task_summary"
```

If I SSH into the server and attach to the tmux session, I'll see something like this.
The top pane is the BOINC client logs; the bottom pane is the current job status.

![A terminal showing a scheduling table of PrimeGrid jobs.]({{ page.imgpath }}/primegrid-tmux.png)

## Step 2: Shop for Spot instances

The AWS Console has some simple tools for looking at current and historical spot prices, but it's fairly limited
in how much data you can view at a time.

I wrote a [quick Ruby tool](https://github.com/hoodlm/JunkDrawer/blob/main/rb/aws/x86_spot_optimizer.rb) for pulling
current EC2 Spot prices across all regions available to my account. The script
prints out something like this, which I can open in a spreadsheet for more analysis.

```
...
For 200 instance types, found 9243 spot prices across 34 regions
$/vCPU/hr  $/hr    instance-type  az         vCPUs  mem (G)
0.00504    0.0403  m6a.2xlarge    eus1-az3   8      32
0.00504    0.0806  m6a.4xlarge    eus1-az3   16     64
0.00504    0.0806  m6a.4xlarge    eus1-az2   16     64
0.00539    0.0431  c7i.2xlarge    apse3-az2  8      16
0.00563    0.0901  c7i.4xlarge    apse3-az1  16     32
0.00564    0.1804  c7i.8xlarge    apse3-az1  32     64
0.00564    0.1804  c7i.8xlarge    apse3-az2  32     64
0.00570    0.1824  c6i.8xlarge    afs1-az3   32     64
...
```

These primality search algorithms are very CPU-intensive, but use very little memory; so when shopping, I'm mostly optimizing
for the $/vCPU/hour column. However, newer generations use newer, faster CPUs; so even though the c7i is 7% more expensive
in this example, it's ~20% faster than the equivalent m6 instance, so it's actually more cost-effective.

Another EC2 Spot protip: I enabled my AWS Account to have access to all available regions (and Local Zones). In this case, some
of the best deals available during the challenge were regions like ap-southeast-3 (Indonesia) and eu-south-1 (Italy)
which are not enabled by default.

## Step 3: Launch some Spot Requests

Once the challenge started, I kicked off a few spot requests; mostly in Indonesia but also a couple other regions.
I ran into a couple issues right off the bat:

### Quotas

EC2 has a default quota of 5 vCPUs per region for new accounts. Although my AWS account was not new, I had only
ever run a few small instances in us-east-2. In some regions, my quotas automatically increased to 64 vCPUs;
but if I wanted a higher limit, I had to open an AWS Support case and wait for a manual approval. (Lesson learned:
scale-test and request limit increases before gametime!)

### Stop-on-interrupt only works for the same instance type and AZ

EC2 Spot really steers you to create Spot fleets with a variety of instance types. For example, if I know I want
64 vCPUs of "c7i" capacity in Indonesia, I don't necessarily want to to request a single c7i.8xlarge in a single AZ;
I have a higher probability of my request being successfully fulfilled, or being re-fulfilled in the case of interruptions,
by requesting any combination of c7i sizes across all AZs in Indonesia.

However: this does not let me efficiently utilize my Stop-on-interrupt preference. If my c7i.8xlarge in apse3-az1 is
interrupted, the instance is stopped and the EBS volume is preserved. But, if my instance is replaced by one in a different
AZ, apse3-az2, the EBS volume *can't* be reused - because EBS volumes are tied to a single AZ.

Effectively, any work that the first instance was doing - even if checkpointed to disk - is wasted work unless I get
lucky and my spot fleet switches back to the original AZ.

So, for my particular use-case, where I'm relying on the ability to stop and resume my spot instances, I'm better off
creating lots of individual requests, each for a single instance types in a single AZ.

# Results!

Even with these issues, I still had a fairly successful challenge. Despite running into quotas, I managed to spend all
of my $60 budget. My spot instances completed over 400 tasks, helping my team (Georgia Tech; long story) place 12th
overall out of 106 participating teams.

About 50 tasks were incomplete - that is, the instance was terminated with tasks still in-progress and could not be resumed
before the end of the challenge. My estimate is that about $5 out of $60 spent was "wasted" on incomplete tasks. (Still much
more cost-effective than paying on-demand EC2 prices!)

For a future challenge, I may try to implement off-host backups of the checkpoints
so that I can move in-flight tasks between instances without losing progress.

And, did I find a prime number? No! In fact, as of writing this post, *no one* working on this PrimeGrid subproject has found any
compositorial primes after checking all numbers from `100000!/100000#` up to `620000!/620000#`.
(That is, all the composite numbers including 620000 multiplied together, which is a ~3 million digit number.)
So, [the search continues](https://www.primegrid.com/stats_cps.php), albeit not on my Spot fleet.
