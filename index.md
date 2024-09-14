---
layout: default
title: Logan Hood
---

# Logan Hood

Software engineer and writer. I blog about technology and non-technology topics!

## Recent posts

{% for post in site.posts limit:4 %}
**{{ post.date | date: "%Y-%m-%d" }}**: [{{ post.title }}]({{ post.url }})<br/>{% endfor %}
[...and more!](./blog.html)

## Links

[GitHub](https://github.com/hoodlm)<br/>
[LinkedIn](https://www.linkedin.com/in/logan-hood-87491b78/)

## Projects

### Software

* [This website](./about.html) is a basic static site created with Jekyll.
* [u](https://github.com/hoodlm/u/tree/main) is an experimental esoteric programming language based around unary operators.
* [my JunkDrawer](https://github.com/hoodlm/JunkDrawer) is a personal monorepo of experimental code and other misfit toys.
* [hoodboy](https://github.com/hoodlm/hoodboy) is a partially-complete GameBoy emulator written in Kotlin (inactive: 2020-2021)
* [Affordable Ultrasound Training Simulator](https://github.com/hoodlm/affordable-medical-ultrasound-training-simulator) was a medical training research project built in Unity3D/C# (inactive: 2012-2014)

### Non-software

* [Music](./music.html)
