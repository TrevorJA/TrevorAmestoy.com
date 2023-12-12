---
title: 'Markdown-Based Scientific and Computational Note Taking with Obsidian'
date: 2022-10-21
permalink: /posts/2022/10/Obsidian/
tags:
  - Obsidian
  - LaTeX
  - Markdown
---

Over the last year, being in the Reed Research Group, I have been exposed to new ideas more rapidly than I can manage. During this period, I was filling my laptop storage with countless .`docx`, `.pdf`, `.txt`, `.html` files semi-sporadically stored across different cloud and local storages.

Finding a good method for organizing my notes and documentation has been very beneficial for my productivity, processing of new ideas, and ultimately staying sane. For me, this has been done through [Obsidian](https://obsidian.md/).

[Obsidian](https://obsidian.md/) is an open-source markdown (`.md`) based note taking and organization tool, with strengths being:

- Connected and relational note organization
- Rendering code and mathematical (LaTeX) syntax
- Community-developed plugins
- Light-weight
- A nice aesthetic

The fact that all notes are simply `.md` files is very appealing to me. I don't need to waste time searching through different local and cloud directories for my notes, and can avoid having OneNote, Notepad, and 6 Word windows open at the same time.  

Also, I like having localized storage of the notes and documentation that I am accumulating. I store my Obsidian directory on GitHub, and push-pull copies between my laptop and desktop, giving me the secutiry of having three copies of my precious notes at all times.

I've been using Obsidian as my primary notetaking app for a few months now, and it become central to my workflow. In this post, I show how Obsidian extends Markdown feature utility, helps to organize topical `.md` libraries, and generally makes the documentation process more enjoyable.  I also try to explain why I believe Obsidian is so effective for researchers or code developers.

<img src="/images/obsidian_pagedemo.png" width = 400 class="center">

# Note Taking in Markdown

Markdown (```.md```) text is an efficient and effective way of not just documenting code (e.g., `README.md` files), but is great for writing tutorials (e.g., in JupyterNotebook), taking notes, and even designing a Website (as demonstrated in [Andrew's Blog Post last week](https://waterprogramming.wordpress.com/2022/09/30/creating-a-collaborative-research-group-lab-manual-with-jupyter-books/)).

In my opinion, the beauty is that only a few syntax tricks are necessary for producing a very clean `.md` document. This is particularly relevant as a programmer, when notes often require mathematical and code syntax.

I am not going to delve into Markdown syntax. For those who have not written in Markdown, I suggest you see the [Markdown Documentation here](https://markdown-guide.readthedocs.io/en/latest/). Instead, I focus on how Obsidian enables a pleasnt environment in which to write `.md` documentation.

# Obsidian Key Features

Below, I hit on what I view as the key strengths of Obsidian:
- Connected and relational note organization
- Rendering code and mathematical (LaTeX) syntax
- Community-developed plugins

## Relational Note Organization

If you take a quick look at any of the Obsidian forums, you will come across this word: *Zettelkasten*.

[Zettelkasten](https://en.wikipedia.org/wiki/Zettelkasten), meaning "slip-box" in German and also referred to as *card file*, is a note taking strategy which encourages the use of many highly-specific notes which are connected to one another via references.  

Obsidian is designed to help facilitate this style of note taking, with the goal of facilitating what they refer to as a "second brain". The goal of this relational note taking is to store not just information about a single topic but rather a network of connected information.

To this end, Obsidian helps to easily navigate these connections and visualize relationships stored within a *Vault* (which is just a fancy word for one large folder directory).  Below is a screen cap of my note network developed over the last ~4 months.  On the left is a visualization of my entire note directory, with a zoomed-in view on the right.


<img src="/images/obsidian_graph.png" width = 400 class="center">


Notice the `scenario discovery` node, which has direct connections to methodological notes on `Logistic Regression`,  `Boosted Trees`, `PRIM`, and literature review notes on `Bryant & Lempert 2010`, a paper which was influential in advocating for [*participatory, computer-assisted scenario discovery*](https://www.rand.org/pubs/external_publications/EP201000192.html).

Each of these nodes is then connected to the broader network of notes and documentation.

These relations are easily constructed by linking other pages within the directory, or subheadings within those pages. When writing the notes, you can then click through the links to flip between files in the directory.

#### Links to Internal Pages and Subheadings
Referencing other pages (`.md` files) in your library is done with a double square bracket on either side:
`[[Markdown cheatsheet]]`

You can link get down to finer resolution and specifically reference various levels of sub-headings within a page by adding a hashtag to the internal link, such as:
`[[Markdown cheatsheet#Basics#Bold]]`

#### Tags and Tag Searches
Another tool that helps facilitate relational note taking is the use of `#tags`. Attach a `#` to any word within any of your notes, and that word becomes connected to other instances of the word throughout the directory.

Once tags have been created, they can be searched. The following Obsidian syntax generates a live list of occurences of that tag across your entire vault:
```
```query
tag: #scenarios
```

Which produces the window:

```query
tag: #scenarios
```


## Rending code and math syntax
### Lanuage-Specific Code Snippets

Obsidian will beatifully styalize code snippets using language-specific formatting, and if you don't agree then you change change your style settings.

A block of code is specified, for some specific language using the tripple-tic syntax as such:
```
```langage
<Enter code here>
```

The classic `HelloWorld()` function can be stylistically rendered in Python or C++:

```python
def HelloWorld():
	"""
	This classic function greets the world.
	"""
	print('Hello world!')
	return
```

```C++
using namespace std;

int main() {
  cout << "Hello World!";
  return 0;
}
```

### LaTeX
As per usual, the `$` characters are used to render LaTeX equations. Use of single-`$` characters will results in in-line equations (`$<Enter LaTeX>$`) with double-`$$` used for centered equations.

Obsidian is not limited to short LaTeX equations, and has plugins designed to allow for inclusion other LaTeX packages or HTML syntaxes.

```latex
$$
\phi_{t,i} = \left\{
	\begin{array}\\
		\phi_{t-1,i} + 1 & \text{if } \ z_t < \text{limit}\\
		0 & \text{otherwise.}
	\end{array}
\right.
$$
```

will produce:
$$
\phi_{t,i} = \left\{
	\begin{array}\\
		\phi_{t-1,i} + 1 & \text{if } \ z_t < \text{limit}\\
		0 & \text{otherwise.}
	\end{array}
\right.
$$

## Plugins

Obsidian boasts an impressive [667 community-developed plugins](https://obsidian.md/plugins) with a wide variety of functionality. A glimpse at the webpage shows plugins that give more control over the visual interface, allow for alternative LaTeX environments, or allow for pages to be exported to various file formats.


<img src="/images/obsidian_plugins.png" width = 400 class="center">

Realistically, I have not spent a lot of time working with the plugins. But, if you are someone who likes the idea of a continuously evolving and modifiable documentation environment then you may want to check them out in greater depth.

# Conclusion: Why did I write this?

This is not a sponsored post in any way, I just like the app.  

When diving into a new project or starting a research program, it is critical to find a way of taking notes, documenting resources, and storing ideas that works for you. Obsidian is one tool which has proven to be effective for me.  It may help you.  

Best of luck with your learning.
