---
layout: post
title: Pure CSS Animated Gradient Background and More!
description: You can do amazing things with pure CSS nowadays, including animations without any Javascript.
date: 2025-08-05 9:29:35 +0300
image: '/images/hella-bg-1.jpg'
tags: [ CSS-tricks ]
tags_color: '#4643ec'
featured: true
---

### CSS Tricks - Pure CSS Animated Gradient Background

Let's take a look at how to create a animated background with no Javascript and just plain CSS.

{: .tip }
Keep your code simple & manageable, with super fast page loads leveraging Pure CSS animations.

Build a lean, mean site without the hassle and overhead of node.js, npm, react/next.js, Vercel etc.

> The best code is no code at all. - Jeff Atwood(Founder of StackOverflow)

---

### Live Example on CodePen

Now let's check out a live example!

{: .note }
CodePen is an amazing free resource for developers!

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="KwdWQqB" data-pen-title="🌈 Pure CSS Animated Gradient Background" data-user="anataliocs" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/anataliocs/pen/KwdWQqB">
  🌈 Pure CSS Animated Gradient Background</a> by Chris Anatalio (<a href="https://codepen.io/anataliocs">@anataliocs</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

You can build super slick features and animations for your site + blog with just Jekyll, Vanilla JS and CSS/Sass hosted on Github Pages(Like this site!) 

[https://github.com/hella-web3/hella-site-base](https://github.com/hella-web3/hella-site-base)

They CSS `@keyframes` rule is the secret sauce for these slick animations that don't require a single line of JS!

**Example below:**
```css
@keyframes gradient {
	0% {
		background-position: 0% 50%;
	}
	50% {
		background-position: 100% 50%;
	}
	100% {
		background-position: 0% 50%;
	}
}
```

Basically, you define specific CSS styles that change over time as the animation progresses.  So you can change the angle, position, color or really anything over the course of
an animation's duration.  Paired with clever use of attributes like `linear-gradient` or using `::before` or after `::after` pseudo-elements with properties like `box-shadow`, this gives you an incredible amount
of power and versatility in creating novel animations and interactivity that can really make your UIs that much more intuitive and just plain fun to use.

When a UI just clicks for users, it really is a joy!

> Programming isn't about what you know; it's about what you can figure out.

And there is just something so intuitive, fun and powerful about figuring out new ways to create cool new effects with just plain old CSS.

{: .note }
Not that long ago it was a pain just to get stuff centered lol

----

### Neon Button Animations

You have so much creative freedom to create all different types of animations and behaviors that don't just look cool but also contribute to a intuitive UX!

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="ZYbKXqV" data-pen-title="Neon Button Animation" data-user="anataliocs" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/anataliocs/pen/ZYbKXqV">
  Neon Button Animation</a> by Chris Anatalio (<a href="https://codepen.io/anataliocs">@anataliocs</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

----

### Much more Fun then a Plain Old Spinner

You can get super creative with really expressive loading animations.

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="jEbBZWZ" data-pen-title="Neon Grid Loaders" data-user="anataliocs" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/anataliocs/pen/jEbBZWZ">
  Neon Grid Loaders</a> by Chris Anatalio (<a href="https://codepen.io/anataliocs">@anataliocs</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

---

### Pure CSS Simulated IDE Coding

This is probably the coolest thing I've seen though.  Simulating an actual IDE with syntax highlighting with just CSS.

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="pvjeaPZ" data-pen-title="Editor Illustration" data-user="anataliocs" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/anataliocs/pen/pvjeaPZ">
  Editor Illustration</a> by Chris Anatalio (<a href="https://codepen.io/anataliocs">@anataliocs</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

Think twice before spinning up a React app next time you have a static site to build.  

I've been a long-time fan of Gatsby... but this super lean stack has really won me over :)

> Clean code is code that is easy to read, easy to understand, and easy to modify. - Robert C. Martin(Uncle Bob)

----

### Macbook Stickers as Dynamic Interactive Elements

At first, glance, it just looks like a img of a Macbook.  But hover over a sticker and you'll see each one is actually a interactive element!

<p class="codepen" data-height="300" data-theme-id="dark" data-default-tab="html,result" data-slug-hash="LEpWQNP" data-pen-title="Personal Concept" data-user="anataliocs" style="height: 300px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid; margin: 1em 0; padding: 1em;">
  <span>See the Pen <a href="https://codepen.io/anataliocs/pen/LEpWQNP">
  Personal Concept</a> by Chris Anatalio (<a href="https://codepen.io/anataliocs">@anataliocs</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://public.codepenassets.com/embed/index.js"></script>

I hope you had as much fun as I did walking through the amazing creativity present in our OSS community <3

It's truly never been a better time to be a developer :D


{: .note }
Hats off to Mozilla and the OSS community for making this stuff happen and hammering away at this stuff until they just really nail it from a developer experience standpoint.
Such a joy to work with and spin up a cute, animated, responsive site in just a couple days :D

----

_What are your favorite Pure CSS animations?  Let me know in the comments below!_

