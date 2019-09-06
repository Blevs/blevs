+++
title = "Wave Function Collapse Explained"
description = """
  A clear, visual explanation of the Wave Function Collapse. Generate
  beautifully emergent images from tiny inputs while learning about a unique
  and fun algorithm.
  """
date = 2019-04-22T04:08:00-04:00
lastmod = 2019-09-03T18:27:00-04:00
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


## In English, Egon {#in-english-egon}

Consider the [Sudoku](https://en.wikipedia.org/wiki/Sudoku), a puzzle
based on a grid where each location can be one of a set of numbers that is
constrained by the values of the other locations. Sound familiar? It is
essentially the same problem we have created for ourselves, but with
different constraints.

Let's try to, hypothetically, solve one and follow along with our algorithm.
We start with a grid where some numbers are already filled in, so we are
partially through 3.

<div class="sudoku">
  <div class="block">
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">2</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">8</div>
    <div class="cell">9</div>
    <div class="cell">
      <div class="candidate">4</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">2</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">4</div>
      <div class="candidate">3</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">8</div>
    <div class="cell">4</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">2</div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">9</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">5</div>
      <div class="candidate">6</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">9</div>
    <div class="cell">1</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">4</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">3</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">4</div>
      <div class="candidate">6</div>
      <div class="candidate">8</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">3</div>
    <div class="cell">1</div>
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">8</div>
    <div class="cell">6</div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
  </div>
  <div class="block">
    <div class="cell">7</div>
    <div class="cell">5</div>
    <div class="cell">4</div>
    <div class="cell">3</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">2</div>
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">3</div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">4</div>
    <div class="cell">2</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">5</div>
    <div class="cell">8</div>
  </div>
  <div class="block">
    <div class="cell">1</div>
    <div class="cell">2</div>
    <div class="cell">8</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">6</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
  </div>
</div>

The first thing we do with a Sudoku is try to find a location where there is
only one possible number. This is 3a, in the case of a single possibility.

<div class="sudoku">
  <div class="block">
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">2</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">
      <div class="new">
        <div class="cell">6</div>
      </div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">8</div>
    <div class="cell">9</div>
    <div class="cell">
      <div class="candidate">4</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">2</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">4</div>
      <div class="candidate">3</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">8</div>
    <div class="cell">4</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">2</div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">9</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">5</div>
      <div class="candidate">6</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">9</div>
    <div class="cell">1</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">4</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">3</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">4</div>
      <div class="candidate">6</div>
      <div class="candidate">8</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">3</div>
    <div class="cell">1</div>
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">8</div>
    <div class="cell">6</div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
  </div>
  <div class="block">
    <div class="cell">7</div>
    <div class="cell">5</div>
    <div class="cell">4</div>
    <div class="cell">3</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">2</div>
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">3</div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">4</div>
    <div class="cell">2</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">5</div>
    <div class="cell">8</div>
  </div>
  <div class="block">
    <div class="cell">1</div>
    <div class="cell">2</div>
    <div class="cell">8</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">6</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
  </div>
</div>

Once we have filled it in, we check the rest of locations to see how the
changes effects the numbers allowed in them. This is 3b, propagation.

We restart. Hopefully, we found a another place with only one possibility.
If this is a hard Sudoku that may not be the case. Our best bet may be a
location where there are two possibilities.

Without knowing any additional Sudoku strategy, we proceed by selecting one
at random. This is 3a and what we mean by _collapsing_ to a single value.

<div class="sudoku">
  <div class="block">
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">2</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">6</div>
    <div class="cell">
      <div class="new">
        <div class="cell">3</div>
      </div>
    </div>
    <div class="cell">8</div>
    <div class="cell">9</div>
    <div class="cell">
      <div class="candidate">4</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">2</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">4</div>
      <div class="candidate">3</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">8</div>
    <div class="cell">4</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">2</div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">9</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">5</div>
      <div class="candidate">6</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">9</div>
    <div class="cell">1</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">4</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">3</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">4</div>
      <div class="candidate">6</div>
      <div class="candidate">8</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">3</div>
    <div class="cell">1</div>
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">8</div>
    <div class="cell">6</div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
  </div>
  <div class="block">
    <div class="cell">7</div>
    <div class="cell">5</div>
    <div class="cell">4</div>
    <div class="cell">3</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">2</div>
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">3</div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">4</div>
    <div class="cell">2</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">5</div>
    <div class="cell">8</div>
  </div>
  <div class="block">
    <div class="cell">1</div>
    <div class="cell">2</div>
    <div class="cell">8</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">6</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
  </div>
</div>

In Sudoku, we would continue the puzzle until we finish the puzzle or hit a
contradiction.

<div class="sudoku">
  <div class="block">
    <div class="cell">5</div>
    <div class="cell">6</div>
    <div class="cell">3</div>
    <div class="cell">8</div>
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">2</div>
    <div class="cell">7</div>
    <div class="cell">1</div>
  </div>
  <div class="block">
    <div class="cell">8</div>
    <div class="cell">4</div>
    <div class="cell">1</div>
    <div class="cell">2</div>
    <div class="cell">7</div>
    <div class="cell">6</div>
    <div class="cell">9</div>
    <div class="cell">3</div>
    <div class="cell">5</div>
  </div>
  <div class="block">
    <div class="cell">2</div>
    <div class="cell">7</div>
    <div class="cell">9</div>
    <div class="cell">1</div>
    <div class="cell">5</div>
    <div class="cell">3</div>
    <div class="cell">8</div>
    <div class="cell">6</div>
    <div class="cell">4</div>
  </div>
  <div class="block">
    <div class="cell">3</div>
    <div class="cell">1</div>
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">8</div>
    <div class="cell">6</div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
  </div>
  <div class="block">
    <div class="cell">7</div>
    <div class="cell">5</div>
    <div class="cell">4</div>
    <div class="cell">3</div>
    <div class="cell">1</div>
    <div class="cell">2</div>
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">9</div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
    <div class="cell">9</div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">1</div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">3</div>
    <div class="cell">7</div>
    <div class="cell">1</div>
    <div class="cell">4</div>
    <div class="cell">2</div>
    <div class="cell">9</div>
    <div class="cell">5</div>
    <div class="cell">8</div>
  </div>
  <div class="block">
    <div class="cell">1</div>
    <div class="cell">2</div>
    <div class="cell">8</div>
    <div class="cell">5</div>
    <div class="cell">9</div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">6</div>
    <div class="cell">3</div>
  </div>
  <div class="block">
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">5</div>
    <div class="cell">3</div>
    <div class="cell">
      <div class="error">0</div>
    </div>
    <div class="cell">
      <div class="candidate">6</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">1</div>
  </div>
</div>

At that point, we would know that we chose the wrong value. We would
_backtrack_ by erasing anything we had filled in up to that point, and
change our selection to the other value.

<div class="sudoku">
  <div class="block">
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">6</div>
    <div class="cell">
      <div class="new">
        <div class="cell">1</div>
      </div>
    </div>
    <div class="cell">8</div>
    <div class="cell">9</div>
    <div class="cell">
      <div class="candidate">4</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">4</div>
      <div class="candidate">3</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">8</div>
    <div class="cell">4</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">5</div>
    </div>
    <div class="cell">2</div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">9</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">5</div>
      <div class="candidate">6</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">3</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">9</div>
    <div class="cell">1</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">4</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">2</div>
      <div class="candidate">3</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">4</div>
      <div class="candidate">6</div>
      <div class="candidate">8</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">3</div>
    <div class="cell">1</div>
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">8</div>
    <div class="cell">6</div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
  </div>
  <div class="block">
    <div class="cell">7</div>
    <div class="cell">5</div>
    <div class="cell">4</div>
    <div class="cell">3</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">2</div>
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">8</div>
    <div class="cell">2</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">6</div>
    <div class="cell">3</div>
    <div class="cell">7</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">4</div>
    <div class="cell">2</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">5</div>
    <div class="cell">8</div>
  </div>
  <div class="block">
    <div class="cell">1</div>
    <div class="cell">2</div>
    <div class="cell">8</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">4</div>
    <div class="cell">6</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">9</div>
    </div>
  </div>
  <div class="block">
    <div class="cell">9</div>
    <div class="cell">4</div>
    <div class="cell">5</div>
    <div class="cell">
      <div class="candidate">3</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
    </div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
      <div class="candidate">6</div>
      <div class="candidate">8</div>
    </div>
    <div class="cell">7</div>
    <div class="cell">2</div>
    <div class="cell">
      <div class="candidate">1</div>
      <div class="candidate">3</div>
    </div>
  </div>
</div>

In WFC, our constraints are much less severe than Sudoku
so we don't hit contradictions very often. Instead of backtracking we
start the whole process over. It is possible to add backtracking to this
algorithm, but that is out of the scope of this article.

With WFC our numbers are going to be colors (but they could be anything),
and our constraints result in an image that is consistent with the input
sample. We will talk about how that works later. The choice of collapsing
the location with the _lowest entropy_ (read least number of choices) not
only reduces contradictions, but makes the process beautiful to watch as it
mimics the human tendency to draw out from what already exists.

That is it. We generate a set of rules, collapse each location to a single
value and propagate the consequences of the collapse with our rules until
every location has a single value.


## Wave Function Collapse {#wave-function-collapse}

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

<div class="overlaps">
  <div>[-1 -1]<div class="pixel-tiles">
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
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
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
    </div>
  </div>
  <div>[0 -1]<div class="pixel-tiles">
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
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
    </div>
  </div>
  <div>[1 -1]<div class="pixel-tiles">
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
      </div>
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
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
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
    </div>
  </div>
  <div>[-1 0]<div class="pixel-tiles">
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
    </div>
  </div>
  <div>[0 0]<div class="pixel-tiles">
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
      </div>
    </div>
  </div>
  <div>[1 0]<div class="pixel-tiles">
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
    </div>
  </div>
  <div>[-1 1]<div class="pixel-tiles">
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
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
    </div>
  </div>
  <div>[0 1]<div class="pixel-tiles">
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
      </div>
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
    </div>
  </div>
  <div>[1 1]<div class="pixel-tiles">
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
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
        <span class="pixel" style="background-color:#697e6dff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
      </div>
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
      <div class="pixel-tile" style="--n: 2">
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
        <span class="pixel" style="background-color:#272822ff"> </span>
        <span class="pixel" style="background-color:#dfba61ff"> </span>
      </div>
    </div>
  </div>
</div>

At no offset, `[0 0]`, we see the tile in question. At `[-1 -1]` are all of
the tiles that overlap when shifted left and up once. In other words, for
each tile in `[-1 -1]`, their bottom right pixel matches the top left pixel
of the tile at `[0 0]`.


#### An Optimization {#an-optimization}

We could move on from here, but it turns out some of this information is
redundant and noticing this provided a key performance improvement to the
algorithm. We don't need to consider any offsets besides left, right, up
and down (at least for the 2d case).

Take a look at `[1 1]`. Note that for each tile it either overlaps
vertically with a tile in `[1 0]`, or horizontally with a tile in `[0 1]`.
This should make sense, as the bottom right pixel in `[0 0]` would need to
match for all three.

Therefore, we don't need to consider the diagonal offsets directly as the
are already constrained by their adjacent, cardinal offsets. When it comes
time to collapse and propagate changes, we only need to consider 4
neighbors instead of 8.

In other words, any changes we make that would effect tiles allowed in a
diagonal offset doesn't need to be handled directly. It will be implicitly
by updating the allowed tiles in the cardinal directions and propagating
its changes left, right, up or down.

If this is confusing, don't worry. We are a little ahead of ourselves, and
it will make more sense when we discuss propagation.


#### Local Similarity {#local-similarity}

All of the tiles in the above overlaps initially seem to fit, but you may
have noticed something odd in `[1 1]`. We noted that for each tile, it
overlaps with either a tile in `[1 0]` vertically, or `[0 1]`
horizontally. In fact, all of the tiles match with both, except for one,
the tile that is all black except for a green pixel on the bottom
left.

Let's select that tile and see what happens.

<div class="pixel-tile" style="--n: 3; max-width: 150px;">
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:transparent"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:transparent"> </span>
  <span class="pixel" style="background-color:#697e6dff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
</div>

While it fits now, try to choose a tile from the `[0 1]` offset.

<div class="pixel-tile" style="--n: 3; max-width: 150px;">
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:transparent"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
  <span class="pixel" style="background-color:#dfba61ff"> </span>
  <span class="pixel" style="background-color:red"> </span>
  <span class="pixel" style="background-color:#272822ff"> </span>
</div>

We get a conflict. The tiles in `[0 1]` necessitate that their bottom right
pixel must be either yellow or black, which is impossible having already
selected the `[1 1]` tile with the green pixel.

This is how the algorithm enforces _local similarity_. What that means is
that this arrangement of pixels is never found in the sample image,
therefore we do not allow it to appear in the output image.

<div class="scale-sharp">

{{< figure src="/img/maze.png" >}}

</div>

We see that there is no place where a yellow pixel borders a green one,
either cardinally or diagonally, hence the contradiction.


### Initializing the Output {#initializing-the-output}

This post is in progress. Check back later for the rest of the algorithm.
