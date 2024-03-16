---
layout: default
title: Logan Hood
---

# Logan Hood

Software engineer and writer. I blog about technology and non-technology topics!

## Recent posts

{% for post in site.posts limit:4 %}
* **{{ post.date | date: "%Y-%m-%d" }}**: [{{ post.title }}]({{ post.url }}){% endfor %}
* [...and more!](./blog.html)

## Links

* [GitHub](https://github.com/hoodlm)
* [LinkedIn](https://www.linkedin.com/in/logan-hood-87491b78/)

## Projects

### Software

* [This website](./about.html)
* [u](https://github.com/hoodlm/u/tree/main), an experimental esoteric programming language based around unary operators.
* [my JunkDrawer](https://github.com/hoodlm/JunkDrawer), personal monorepo of experimental code and other misfit toys.
* [hoodboy](https://github.com/hoodlm/hoodboy), GameBoy emulator written in Kotlin (inactive: 2020-2021)
* [Affordable Ultrasound Training Simulator](https://github.com/hoodlm/affordable-medical-ultrasound-training-simulator), Unity3D/C#, medical training research project (inactive: 2012-2014)

### Non-software

* [Music](./music.html)
