---
layout: post
title: "PrimeGrid on EC2 Spot: Bonus IPv6 Adventures"
excerpt: "Using IPv6 to save money and discovering that, of course, not everyone supports IPv6 in 2025."
imgpath: "/assets/img/posts/primegrid-1"
---

In my [last post](/2025/10/03/primegrid-2-ec2-spot.html), I talked about running PrimeGrid workers on EC2 Spot.
As you remember I had a $60 budget to work with, and I wanted to get as many
imaginary internet points as I could out of that $60.

Without going down too much of a rabbithole: computers on the internet can use either IPv4 or IPv6 addresses to talk to
each other. IPv4 has been around longer and is more widely supported, but there's a scarcity of IPv4 addresses, so some
technology companies like AWS *really* want people to move to IPv6.

What this means for me, a penny-pinching EC2 Spot Enjoyer: I can save 
[$0.005 an hour per instance](https://aws.amazon.com/blogs/aws/new-aws-public-ipv4-address-charge-public-ip-insights/)
by using IPv6 instead of IPv4.

As always, it's a question of whether everything I need to talk to *also* supports IPv6.

### Primegrid: Yes

PrimeGrid itself does support IPv6. (thanks Rytis and team!)

```
> ping -c1 -6 primegrid.com
PING primegrid.com (2a01:4f9:3080:4141::2) 56 data bytes
64 bytes from 2a01:4f9:3080:4141::2: icmp_seq=1 ttl=47 time=114 ms
```

### BOINC software repo: No

But, my userscript also connects to Berkeley's website to install BOINC - and, unfortunately,
UC Berkeley, boasting America's second best Computer Science program for undergraduates and graduates
as rated by USNews.com in 2025, does not support IPv6 on any of their domains in 2025.

```
> ping -c1 -6 berkeley.edu
ping: berkeley.edu: Address family for hostname not supported
```

### github?

No big deal; BOINC also has release binaries on Github.

```
> curl --ipv6 https://github.com/BOINC/boinc/releases/download/client_release%2F8.2%2F8.2.5/boinc-manager_8.2.5-2581_amd64_noble.deb
curl: (6) Could not resolve host: github.com
```

([I'm late to this party, apparently.](https://doesgithubhaveipv6yet.com/))

### Solution: host the Boinc client myself

I downloaded the BOINC package onto my personal computer 
then uploaded it into a private S3 bucket in my account.
When my instances start up, they can pull it from that bucket instead.
So, here's the updated section of my userdata script:

```
### Install BOINC and tmux

# get tmux and various dependencies from standard ubuntu repos
apt install -y tmux libxss1 x11-common

# install AWS CLI (which now is distributed in ubuntu as a snap, not an rpm/deb)
snap install aws-cli --classic

# Force aws cli to use IPv6 to connect to S3
aws configure set default.s3.use_dualstack_endpoint true

# download BOINC, prestaged in an S3 bucket
aws --region "us-east-2" s3 cp "s3://${BUCKET}/boinc-client_8.2.5-2581_amd64_noble.deb" .
dpkg --install boinc-client*.deb
```

