---
layout: ../../layouts/MarkdownPostLayout.astro
title: "Boggle"
publicationDate: "2026-07-01"
description: "An online version of the game Boggle."
---

Boggle is an online version of the game Boggle, a word game in which players try to find as many words as they can from a grid of lettered dice.

## Why I Built It

I always loved word games. I discovered Boggle through and got hooked.

The site I used to play it had used AI images and the UI was quite unclear, so I challenged myself to recreate it as a small project.

After making a tightly scoped prototype, I decided to have a proper crack at the project to improve the UI and include many more settings.

This would be the first game-style website I had ever made, so it was a good challenge to get back into the swing of things.

## How It Works

The bulk of this game is of course in the JavaScript logic, neatly packaged into different components to help build the base rules.

Most of the logic is relatively simple - updating UI components, tracking states, and building utilites. The more interesting parts of this project was in the necessity of using a Trie.

A trie is an ordered tree datastructure typically used to store and retrieve strings from a dictionary. To play Boggle, we have to find words in a randmomly generated grid of dice. Instead of checking that a word is valid when it is guessed, ew can use Tries to essentially build a Boggle solver to give us all potential solutions before the user even starts playing.

The main benefit of Tries in this process is the ability to optimise the depth-first search by skipping word paths that have an invalid index. This would simply not be possible without a Trie, and helps speed up the code considerably (especially for larger grids).

Beyond the solver itself, the rest of the code is relatively simple. The bulk of the remaining logic is for interacting with the board, allowing for both clicking and dragging while keep it keyboard accessible. My solution, in isolation, can be found on Codepen and was slightly adapted for this game.

It works by tracking a few variables in a selection state, and mapping this onto an internal representation of the button grid.

## What I Learned

This project improved my understanding of:

- Seperation of concerns with components in JavaScript
- Tailwind styles with data attributes
- Learning JSDoc more
- New ways of writing better guarding for variables types
- Using Tries and common etiquette for making contributions to open source projects

## Future Improvements

Potential areas for future development include:

- More clear UI components for certain interactions (for example, a disabled state on settings buttons or a clearer indication for scrolling on correct guesses)
- Improved dragging grid interaction - currently the diagonal movement is sometimes annoying
- A better dictionary (using OSPD4 was easy, but it includes many uncommon words making each board unsolveable)
- Improved 6x6 grid gameplay (particularly making use of the blank die sides for more words)

## Links

- [Boggle Repository](https://github.com/dalton-f/boggle)
- [Boggle](https://dalton-f.github.io/boggle/)
- [Adjacent Button Selection](https://codepen.io/dalton-f/pen/JobNGby)
- [2D Grid Pathfinding (without Trie pruning)](https://codepen.io/dalton-f/pen/RNooxQN)
