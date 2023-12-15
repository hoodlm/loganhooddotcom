---
layout: post
title: "Everyday peer-to-peer sharing with 7-Zip and BitTorrent"
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

## BitTorrent for file-sharing. No, really!

It's remarkably hard to send large files between PCs over the Internet -
or, even on the same network - without using someone else's computer
(aka The Cloud) as an intermediary.

The BitTorrent protocol is still the gold standard for peer-to-peer file
sharing. Most people have used BitTorrent at some point to download (and hopefully seed)
a torrent. Where did that torrent come from? Someone created it! It's actually easy to
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
* [OpenTrackr](http://tracker.opentrackr.org/) is the free public tracker I recommend.
You can check [ngosang/trackerslist](https://github.com/ngosang/trackerslist) to find
other options if needed.

### Security disclaimer

The methodology described in this blog post may provide adequate privacy for sharing
personal, low-stakes data over the Internet. It's a convenient way to share
photos/videos, but I would not use it for anything "important".
Please don't use this for backing up a PII dataset or sharing your country's nuclear codes.

## How-to

At a high level, the steps are:

1. Create a password-protected (encrypted) 7z archive containing the file(s) you want to share.
2. Create a .torrent file from that 7z archive.
3. Send the .torrent file to the recipients. (This is a small file, and doesn't need to be kept private.)
4. Securely send the password to the recipients. (The password **does** need to be kept secret!)
5. Leave your torrent seeding until all recipients have downloaded it.

### (1) Create an encrypted 7z archive

Put all the files you want to share into the same folder.
If you've installed 7Zip, you should be able to right-click on the folder and have an
option to compress as a 7z file. Here's what this looks like in Mint Linux, for example:

![Right click on PinkWojaks folder, mouse-over the Compress option](/assets/img/posts/file-sharing-with-torrents-no-really/right-click-compress.png)

Make sure that you choose **7z** as the file format, and that you use a strong password.
You need to keep the password somewhere, because you'll have to share this later.
Optionally, you can change the filename (like I've done here) if you want to hide the
original folder name.

![Dialog to compress the directory. Filename: burly-duckbill-aviation, Password: "GMsDvFPibiwCRpnTxTgKyRi", check "Encrypt the file list too"](/assets/img/posts/file-sharing-with-torrents-no-really/compress-dialog.png)

### (2) Create a .torrent file

qBittorrent includes a torrent creator wizard, under Tools -> Torrent Creator.

Choose your 7z file, "Start seeding immediately", and
add a tracker URL. In this case I'm using `udp://tracker.opentrackr.org:1337/announce`.

![qBittorrent user interface, with the 7z file selected as the file path, 'start seeding immediately' checked, private torrent NOT checked, and tracker URLs populated with a tracker announcement URL.](/assets/img/posts/file-sharing-with-torrents-no-really/create-torrent-dialog.png)

#### Security note 1!

Take note that I did not check the 'Private Torrent' option - because we **do** want
to enable the "DHT network" so that recipients can auto-discover your IP address. The
distinction here is that the torrent file *itself* does not need to be private; the
actual contents of the torrent - the encrypted 7z file - keeps our original content private.

In layman's terms: at this point, anyone who gets the .torrent file can make your computer upload
your 7z file to them. But, that's perfectly OK: because you used a strong password when you created
the 7z file, an unintended recipient can't open if they download it, because they don't know
and won't be able to guess your password.

### (3) Send the .torrent file to the recipients

You can use email or anything you want. Remember, the .torrent file does not need
to be kept secret. You can download mine [right here](/assets/bin/burly-duckbill-aviation.7z.torrent).

### (4) Securely send the password to the recipients

The level of security here depends on the level of privacy you need. My recommendation
is to use a messenger service that supports end-to-end encryption, like WhatsApp or
Telegram or Signal. But, if you would have emailed the contents of the torrent anyway
if they were small enough to fit in an email attachment, you can use email or text
message to share the password.

### (5) Leave your torrent seeding until all recipients have downloaded it

Your recipients should be able to open the torrent and download the 7z file. When they open the 7z 
archive, they'll be prompted to enter the password you shared with them.

You have to leave qBittorrent running on your computer to "seed" so that
everyone can download it from you. You'll see your recipients
show up as "peers" while they are downloading.

Finally, if you are sharing the file with many recipients, remember that you can
ask them to leave their torrent program open, and they'll be able to seed to each other.

## Other questions

### I don't care about privacy. Can I skip the encryption step?

Yes, if your goal is to share files, and you don't really care if someone else gets them
or sees what you're sharing, then you can skip the password-protection and encryption.
And if you are not encrypting the files, it's OK to skip 7-Zip altogether and
just create a torrent directly from a folder.

### Can I use 7z/Torrent to send files to a mobile device too?

Yes, on Android, you can use LibreTorrent and ZArchiver. There are probably analogous apps available for iOS.

### Can I use .zip instead of .7z?

I don't recommend it because the encryption scheme used on .zip files is not very secure.
It can be cracked by a determined attacker, even if they don't guess your password.
