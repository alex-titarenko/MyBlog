---
id: cd3f67ec-5eb6-4cfd-98f6-17a40c0b2969
published: false
postedOn: 05/30/2021
description: ""
---

## Introduction

Hello my friend. May I assume that some of you also write blog posts? Then the next question for you, how do you author them, what tool/platform do you use? WordPress, Blogger, Medium, anything else?
Since I'm software developer and not always try to find easy and straightforward path, I developed my own simple blogging system where all of the blog posts are stored in Git repository as markdown files. During the build process they will be compiled into HTML files to easy serve from CDN. In this approach there is one single problem, it may not be the most pleasant way to change or create blog post from VS Code or GitHub website.
Luckily, my recently developed tool [NotesHub](https://noteshub.app) solved this puzzle, and this post is meant to open the curtain behind my new project.

## Birth of the idea

I started thinking about the idea of my new side project in late Fall 2020. The main driver was the dislike of some core principals of OneNote, at that moment the primary note-taking app for my personal needs.
I like everything to be in order, to match styles and it's not an easy task when you deal with such apps like OneNote. When you copy-paste a text from the browser it will preserve all the styles including font colors, font sizes, etc. As a result you will end-up when all of your notes look different. You may see echoing of style preserving also when you look at your notes on mobile device, and see horizontal scroll to appear, where I would only expect vertical scrolling. The second annoying thing for me is that it's too easy to change something, I had several times when I changed the notes by accident and then realizing it too late. Don't put me wrong, OneNote is amazing application. It has a lot of strengths like supporting real-time collaboration, writing with stylus and many more, however those benefits are not really important for me and disadvantages appeared to overweight everything else.

I started looking for alternatives. Since I'm already familiar with Markdown, it become the first think to come into my mind as format for notes. Markdown solves the problem with styles, since it has pretty limited formatting options. For instance you can't change font face, color, size just with syntax itself, that is exactly what I needed.
By searching in App Store I found GitJournal application which kind of what I was looking for. When I showed it for my wife she told that it's not good enough to pay for it. My response was: "Right, challenge accepted, I'll write my own app". That event become the starting point for NotesHub project.

## Implementation

Since I'm huge fan of [PWA](https://en.wikipedia.org/wiki/Progressive_web_application) and have experience with creating such application I had no doubt what technology to use. In October 30, 2020 I made my first commit.
Initially I planed to use only GitHub API for working with GitHub, but that I quickly realized all limitations. I would not be able to fully implement offline use-cases like editing and creating notes without network connectivity. With some Googling I found https://isomorphic-git.org open-source project which I could use as Git client in the browser and that will enable range of possibilities compare with just GitHub API:
* Much faster Notebooks cloning
* Full offline support
* Ability to do 3-way merge of notes
* Faster sync operation

In the last day before going to vacation in Cancun, I made the final commit to finalize MVP version of the project and ending the 6 month journey from the idea to first stable version. Project was finally available to public.

## The road ahead
