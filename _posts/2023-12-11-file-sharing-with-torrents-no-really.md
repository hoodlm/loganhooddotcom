---
layout: post
title: "File Sharing with BitTorrent: No, Really!"
---

#### > Can you email those photos to me?

Sorry, the entire folder is 5GB, and my email service only allows 25MB attachments.

#### > What about Google Drive?

Didn't you see what [Senator Wyden had to say](https://www.documentcloud.org/documents/24191267-wyden_smartphone_push_notification_surveillance_letter_to_doj_-_signed)
about Google? I can't put my wedding photos there!

Maybe you can just install an SFTP client on your end? I'll need you to also
generate an Ed25519 key; then just send me your public key on Signal and I'll get you all set up.

#### > Nevermind, I'll just come over with a flash drive...

### BitTorrent: easier than you think

It's remarkably hard to send large files between PCs over the Internet
(or, even on the same network) without using someone else's computer as an intermediary.

The BitTorrent protocol is still the gold standard for peer-to-peer file
sharing. Most people have used BitTorrent at some point to download (and hopefully seed)
a torrent that someone else has created - but it's very easy to create your own torrent,
even just to send to one or two other people. You can use BitTorrent as a self-hosted
means to share files like photos or videos, with decent privacy, and it's simple enough
to use with moderately tech-savvy friends and family.

The toolchain is [7-Zip](https://7-zip.org/download.html) to archive and encrypt
the files you want to send, then [qBittorrent](https://www.qbittorrent.org/download) to
create the torrent file and post it to a tracker. The people you are sharing with use
the same tools in the opposite order: qBittorrent to download, and 7-Zip to decrypt
and un-archive.

(7-Zip and qBittorrent are the best cross-platform, free programs I know of at the
time of writing this post.)

### Important Security Notes

The methodology described provides adequate privacy for sharing personal, low-stakes
data over the Internet. It's a convenient way to share photos/videos, but I would not
use it for anything "important". Please don't use this for backing up a PII dataset or
sharing your country's nuclear codes.

## How-to

TODO
