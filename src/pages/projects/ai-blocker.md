---
layout: ../../layouts/MarkdownPostLayout.astro
title: "AI Blocker"
publicationDate: "2026-05-07"
description: "A browser extension that blocks users from accessing LLMs in various ways"
---

AI Blocker is an open-sourced browser extension that blocks/hides the typical AI overview that users get when making any Google search.

## Why I Built It

Using the web has gotten a lot worse with the constant injection of AI into tools that really don't need it.

By hiding this typical AI overview LLM snippet from search results, this extension aims to greatly improve user's quality of life when making searches and speeds up their workflow by removing an unnecessary feature that typically gets in the way or can provide misinformation.

This extension also has features which redirect users away from any of the most popular generative AI tools and websites, including ChatGPT and DALL-E in order to spread information about the dangers and issues with AI while helping people break a reliance on these tools.

## How It Works

This tool uses a range of JavaScript files and a manifest.json setup which redirects users away from the most popular AI and LLM tools.

It also uses a MutationObserver to target the AI overview container that gets used for most searches, hiding it across all tested browsers for a range of languages.

This tool also hides sponsored search results if the user does not have an ad-blocker which handles this functionality already.

## Challenges

One of the main challenges was learning how to use manifest.json when it comes to browser extension scripts and finding as many URL matches to block a variety of tools.

Another challenge I faced with this project was ensuring it had all the correct details and requirements filled out to be published across browser extenstion stores.

## What I Learned

This project improved my understanding of:

- MutationObservers in JavaScript
- Using a range of selectors to search for the correct HTML elements with limited information
- RegEx patterns for making the tool work across languages

## Future Improvements

Potential areas for future development include:

- Including a larger range of languages to work for international users
- Continue to expand the websites and tools that the script matches to keep up with evolving AI tools
- Fix issues opened in the repo, particularly with the script not working across Firefox.

## Links

- [AI Blocker Repository](https://github.com/dalton-f/ai-blocker)
- [AI Blocker on FireFox addons store](https://addons.mozilla.org/en-US/firefox/addon/google-ai-blocker/)
- [AI Blocker on Chrome Webstore](https://chromewebstore.google.com/detail/ai-blocker/facdafkajoefgohbeaoekodeemffmlhi)
