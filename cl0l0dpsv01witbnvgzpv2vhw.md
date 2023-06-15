---
title: "What is Emmet In Web Development"
seoTitle: "What is Emmet? Why should you use Emmet?"
seoDescription: "Emmet is a plugin for many popular text editors which helps in writing HTML code for your webpage. Emmet helps in fast coding."
datePublished: Thu Mar 10 2022 13:11:15 GMT+0000 (Coordinated Universal Time)
cuid: cl0l0dpsv01witbnvgzpv2vhw
slug: what-is-emmet-in-web-development
cover: https://cdn.hashnode.com/res/hashnode/image/unsplash/oXlXu2qukGE/upload/v1646913379682/0C2fGRviU.jpeg
tags: css, web-design, web-development, html, html5

---

Once, I was watching a frontend design tutorial on YouTube and noticed that the tutor was typing HTML code really fast. When I looked carefully, I saw him typing some snippet like code which turned into HTML code automatically. Later, he mentioned that the code he was writing was Emmet. I then did some research on my own to learn about Emmet and was fascinated by it. So, now let me explain to you what Emmet actually is through this blog. 

## INTRODUCTION
Emmet is a plugin for many popular text editors which helps in writing HTML code for your webpage. As mentioned in its official website, it is an essential toolkit for web-developers. Many text editors allow the user to store some commonly used code blocks which can later be reused. These code chunks are called snippets and help to boost productivity. But the main issue with snippets is that they should be manually defined by user and can't be extended in runtime. Emmet takes this concept to the next level, where snippet codes are CSS-like expressions that are dynamically parsed and output depends on what is typed in Emmet abbreviation. The syntax of these abbreviations are inspired by CSS and these abbreviations are parsed in runtime transformed into a structured HTML code block. Emmet is specially developed and optimized for web-developers whose workflow depends on HTML/XML and CSS, but can be used with other programming languages too. As Emmet is written in pure JavaScript, so it works across different platforms like web browsers, Node.js, Microsoft WSH etc.

## WHY SHOULD YOU USE EMMET?

- As I told earlier, syntax for writing an Emmet code is similar to CSS selectors, so web-developers can easily learn and use it.
- Emmet helps in fast coding. You can easily and quickly write a bunch of code, wrap code with new tags, quickly traverse and select important code parts, match tag pair, split/join tag, remove tags, update image size, evaluate math expression, increment/decrement number etc.
- Emmet is supported in many text editors like VS Code, Sublime Text, Eclipse, Espresso etc. VS Code which I prefer has this plugin pre-installed, so anyone can go and start using them. In other editors, you may have to install it manually, but it is super simple to do so.
- Emmet is Open Source, so you can check its code in GitHub and even modify the code according to your will.

## HOW TO USE EMMET?
I will now show you an example of how you can write a big chunk of code using a single line of Emmet.

### HTML Code:

```
<div id="home">
	<div class="logo">LOGO</div>
	<ul id="navbar">
		<li class="navitem"><a href="" class="navlink">Item 1</a></li>
		<li class="navitem"><a href="" class="navlink">Item 2</a></li>
		<li class="navitem"><a href="" class="navlink">Item 3</a></li>
		<li class="navitem"><a href="" class="navlink">Item 4</a></li>
		<li class="navitem"><a href="" class="navlink">Item 5</a></li>
	</ul>
</div>
``` 

### Emmet Abbreviation Code

```
#home>div.logo{LOGO}+ul#navbar>li.navitem*5>a.navlink{Item $}
``` 
Here, in this Emmet abbreviation every symbol has a meaning.  
- ' > ' means **Child** element
- ' + ' means **Sibling** element
- ' . ' represents **CSS Class**
- ' # ' represents **CSS ID**
- ' {} ' represents **text** of an element 
- ' * ' represents __multiplication__  
- ' $ ' represents **numbering**

There are many other such symbols and syntaxes, and you can learn more about it from this cheat sheet.  
Emmet Cheat sheet: [Emmet Cheat Sheet](https://docs.emmet.io/cheat-sheet/) 

## CONCLUSION
After reading this blog, I hope that you have now some more understanding about Emmet. So to conclude, Emmet makes web development much easier and I hope you all will be using this wonderful toolkit. If you want to learn more about it, do check references section. Feel free to comment below to express your thoughts. 

## REFERENCES
1. [Emmet Official Website](https://emmet.io/)
2. [Emmet Cheat Sheet](https://docs.emmet.io/cheat-sheet/)
3. [Emmet GitHub](https://github.com/emmetio/emmet)


