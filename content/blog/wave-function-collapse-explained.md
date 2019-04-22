+++
title = "Wave Function Collapse Explained"
description = """
  A clear, visual explanation of the Wave Function Collapse. Generate
  beautifully emergent images from tiny inputs while learning about a unique
  and fun algorithm.
  """
date = 2019-04-22T04:08:00-04:00
tags = ["wfc"]
draft = false
+++

If you are not familiar, [Wave Function Collapse](https://github.com/mxgmn/WaveFunctionCollapse) (WFC) is an algorithm that
generates images that are locally similar to input images.

In general, the algorithm that randomly generates an input that follows rules
regarding the adjacency of each element.


## Bird's Eye View {#bird-s-eye-view}

Here is the basic operation of the algorithm:

1.  Generate a set of tiles from an input image.
2.  Determine which tiles may overlap with each other, generating a set of
    rules for tiles that can be neighbors.
3.  Generate an output image where every pixel can be every tile (a _superposition_).
    a. Choose a pixel with the smallest number of possible tiles, and
    _collapse_ it to a single tile.
    b. Propagate the collapse by checking the pixel's neighbors for tiles
    that are no longer allowed to be there based on our overlapping rules.
    Repeat until there are no more changes.
    c. Goto a
4.  When every pixel is a single value we have finished generating our output
    image. Sometimes, we will reach a contradiction. In this case, we restart
    at 3.

Based on the rules we generated every pixel and its neighbors will be in a
configuration found in our initial input. This is what we mean when we say
that our output image is _locally similar_ to our input.

```clojure
(use 'hiccup.core)
(defn pphtml [content]
  (let [in (javax.xml.transform.stream.StreamSource.
            (java.io.StringReader. (html content)))
        writer (java.io.StringWriter.)
        out (javax.xml.transform.stream.StreamResult. writer)
        transformer (.newTransformer
                     (javax.xml.transform.TransformerFactory/newInstance))]
    (.setOutputProperty transformer
                        javax.xml.transform.OutputKeys/INDENT "yes")
    (.setOutputProperty transformer
                        "{http://xml.apache.org/xslt}indent-amount" "2")
    (.setOutputProperty transformer
                        javax.xml.transform.OutputKeys/METHOD "xml")
    (.setOutputProperty transformer
                        javax.xml.transform.OutputKeys/OMIT_XML_DECLARATION, "yes")
    (.transform transformer in out)
    (-> out .getWriter .toString print)))
```

```clojure
(pphtml [:div [:span "test"]])
```

<div>
  <span>test</span>
</div>


## An Example {#an-example}

Let's perform the algorithm to get a better feel of how it work. We are
going start with a an image.

<div class="scale-sharp">

{{< figure src="/img/maze.png" >}}

</div>


### Making the Rules {#making-the-rules}

Let's begin by splitting our image into pixels. This article's scope is only
to explain how the algorithm works, but you can check the source of this
article to see the code used to generate all of the following images.
However, it will be improved and properly explained in the next article out
our implementation.

<div class="pixel-tile" style="--n: 4">
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#697e6dff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
</div>

With our sample image, the next step is divide it into tiles, in this case
2 pixel by 2 pixel regions.

In this case, we have chosen to have our tiling wrap. When our tile
selector hits an edge, it will get the pixels on the opposite side. This is
one of the ways we increase the number of tiles in an input image to
improve our output. Other methods include rotating and reflecting each tile
to get even more.

<div class="pixel-tiles">
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#697e6dff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#697e6dff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#697e6dff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#697e6dff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
  </div>
  <div class="pixel-tile" style="--n: 2">
    <span class="pixel" style="background-color:#272822ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
    <span class="pixel" style="background-color:#dfba61ff"> </span>
  </div>
</div>

Quite a few of the tiles above are repeated and WFC makes use of that
information. Tiles that more frequently appear in the sample more frequently
appear in the output. When _collapsing_ a pixel to a single tile out of all
the possible tiles, the probability for each one to be selected is weighted
by its frequency in the sample image.

Above, it is obvious that the neighboring tiles are able to overlap. We
want to know, for every tile, which tiles it can overlap with in all
directions. Let's take a look at the first tile.

This post is in progress. Check back later for the rest of the algorithm.
