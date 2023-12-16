---
layout: post
title: "Everyday peer-to-peer sharing with 7-Zip and BitTorrent"
imgpath: "/assets/img/posts/everyday-7zip-bittorrent"
---

#### > Can you email those photos to me?

Sorry, the entire folder is 5GB, and my email service only allows 25MB attachments.

#### > What about Google Drive?

I'm at 90% of my Google Drive quota. Besides, didn't you see what
[Senator Wyden had to say](https://www.documentcloud.org/documents/24191267-wyden_smartphone_push_notification_surveillance_letter_to_doj_-_signed)
about Google? I can't put my wedding photos there!

Maybe you can just install an SFTP client on your end? I'll need you to also
generate an Ed25519 key; then just send me your public key on Signal and I'll get you all set up.

#### > Nevermind, I'll just come over with a flash drive...

## You can use BitTorrent for file-sharing. (It's not just for pirates.)

Sometimes, it's surprisingly frustrating to send large files between PCs over the Internet -
or, even on the same network - without using someone else's computer
(aka The Cloud) as an intermediary.

The BitTorrent protocol is the gold standard for peer-to-peer file
sharing. You probably have used BitTorrent at some point to download (and hopefully seed)
a torrent. Where did that torrent come from? Someone created it!

It's actually easy to
create your own torrent, even just to send to one or two other people.
You can use BitTorrent as a self-hosted
means to share files like photos or videos, with decent privacy, and it's simple enough
to use with moderately tech-savvy friends and family.

The toolchain is [7-Zip](https://7-zip.org/download.html) to archive and encrypt
the files you want to send, then [qBittorrent](https://www.qbittorrent.org/download) to
create the torrent file and post it to a tracker. The people you are sharing with use
the same tools in the opposite order: qBittorrent to download, and 7-Zip to decrypt
and un-archive.

### As of {{ page.date | date: "%Y-%m-%d" }}...

As always, things may change; if this post is old, you need to do research to make
sure that what was true in late 2023 is still true:

* 7-Zip and qBittorrent are the best cross-platform, free programs I know of at the
time of writing this post. The instructions/screenshots are based on 7-Zip v16.02 and
qBittorrent v4.4.1.
* [OpenTrackr](http://tracker.opentrackr.org/) is a free public tracker I recommend.
You can check [ngosang/trackerslist](https://github.com/ngosang/trackerslist) to find
other options if needed.

**Security disclaimer**: The methodology described in this blog post provides adequate privacy for sharing
personal, low-stakes data over the Internet.
Please don't use this for backing up your employers' PII dataset or sharing
your country's nuclear codes.
{: class="serious-security-business"}

## How-to

At a high level, the steps are:

1. Create a password-protected (encrypted) 7z archive containing the file(s) you want to share.
2. Create a .torrent file from that 7z archive.
3. Send the .torrent file to the recipients. (This is a small file, and doesn't need to be kept private.)
4. Securely send the password to the recipients. (The password **does** need to be kept secret!)
5. Leave your torrent seeding until all recipients have downloaded it.

### (1) Create an encrypted 7z archive

Put all the files you want to share into the same folder.
If you've installed 7-Zip, you should be able to right-click on the folder and have an
option to compress as a 7z file. Here's what this looks like in Linux Mint, for example:

![Right click on PinkWojaks folder, mouse-over the Compress option in the drop-down menu]({{ page.imgpath }}/right-click-compress.png)

Choose **7z** as the file format and use a strong password.
You need to write down the password somewhere, because you'll have to share it with your recipients.
Optionally, you can change the filename (like I've done here) if you want to hide the
original folder name.

![Dialog to compress the directory. Filename: burly-duckbill-aviation, Password: "GMsDvFPibiwCRpnTxTgKyRi", check "Encrypt the file list too"]({{ page.imgpath }}/compress-dialog.png)

### (2) Create a .torrent file

qBittorrent includes a torrent creator wizard, under Tools -> Torrent Creator.

Choose your 7z file, "Start seeding immediately", and
add a tracker URL. In this case I'm using `udp://tracker.opentrackr.org:1337/announce`

![qBittorrent user interface, with the 7z file selected as the file path, 'start seeding immediately' checked, private torrent NOT checked, and tracker URLs populated with a tracker announcement URL.]({{ page.imgpath }}/create-torrent-dialog.png)

Take note that I did not check the 'Private Torrent' option - because we **do** want
to enable the "DHT network" so that recipients can auto-discover your IP address.

The distinction here is that the torrent file itself does not need to be private.
The torrent file doesn't contain the 7z file (your actual data) - it just contains
a "pointer" to tell other computers where to download the file from.

The 7z file contains the original data, but thanks to the mathematical wonders of
cryptography, the encrypted bits are still useless to someone that doesn't have your password!

Let's frame this as a Threat Model at this point. There's a "bad guy" whose objective
is to try to read the original data. Suppose that he intercepts the .torrent file and
uses it to download the encrypted 7z file.
That's perfectly OK: because you used a strong password when you created
the 7z file, the unintended recipient still can't open it, because he doesn't know
and won't be able to guess your password.

### (3) Send the .torrent file to the recipients

You can use email or any means to share the .torrent file. Remember, the .torrent file does not need
to be kept secret.

### (4) Securely send the password to the recipients

The level of security here depends on the level of privacy you need. If you want
to be paranoid, you can use a messenger service that supports end-to-end encryption, like WhatsApp or
Telegram or Signal. (But, if you would have emailed the original file(s) anyway,
if they had been small enough to fit in an email attachment, you would also be OK with sharing the
password in an email.)

### (5) Leave your torrent seeding until all recipients have downloaded it

Your recipients should be able to open the torrent and download the 7z file. When they open the 7z 
archive, they'll be prompted to enter the password you shared with them.

![password required dialog window]({{ page.imgpath }}/password-required.png)

And then the files are there!

![various pink wojaks in an extracted folder]({{ page.imgpath }}/mission-accomplished.jpg)

You have to leave qBittorrent running on your computer to seed so that
everyone can download it from you. You'll see your recipients
show up as peers while they are downloading.

Finally, if you are sharing the file with many recipients, remember that you can
ask them to leave their torrent program open, and they'll be able to seed to each other.

### I don't care about privacy. Can I skip the encryption step?

Yes, if your goal is just to share files, and you don't really care if someone else gets them
or sees what you're sharing, then you can skip the password-protection and encryption.
And if you are not encrypting the files, it's OK to skip 7-Zip altogether and
just create a torrent directly from a folder.

### Can I send files this way to someone who only has a smartphone?

On Android, you can use LibreTorrent and ZArchiver. There are probably analogous apps available for iOS.

### Can I use .zip instead of .7z?

I don't recommend it because the encryption scheme used on .zip files is not very secure.
It can hypothetically be cracked by a determined attacker, even if they don't guess your password.
