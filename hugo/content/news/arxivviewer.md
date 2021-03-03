---
title: "My tool for Viewing arXiv Papers"
date: 2021-02-24T18:53:26-05:00
draft: false
tags: [arxiv,webapp]
Description: "Use the viewer here https://arxiv.gtflashlab.com/"
---

Recently, the hundreds of daily updates from arXiv make me feel overwhelmed. To help me better digest new machine learning papers, I developed an arXiv paper reading web app: https://arxiv.gtflashlab.com/ .

It gives me an Outlook-like user experience. It allows me to view the latest papers in an efficient, manageable, personalized way.
It also provides some basic functions, that help me to manage these papers:
- Reading status: it tells me whether I have viewed the paper or not.
- Archive: I can archive papers that I don't want to see anymore.
- Star: Save my favorite papers.
- Tagging: it allows me to add tags to categorize the papers.
- Tagging from abstract/title: I can efficiently add tags from the title/abstract by simply selecting the text.
- Filtering by Reading status/Archive status/Star/Tags
- Read Abstract/PDF/HTML
- PDF Annotation which can be saved on the server.
- Search: Full-text-search on title and abstract (If you can't find the paper, add "&&searchEngine=simple" to the URL. It will perform a case-insensitive exact-match search, which is kind of slow. Don't blame me for the search experience, I am a newbie to the database. )
Citation/Reference (based on semantic scholar)
- View papers on your iPhone/iPad by saving the website as a bookmark on the homepage. (Although some things are still malfunctioning. /(ㄒoㄒ)/~~)

I want to share it with anyone who might find it useful for doing research.

**Acknowledge**
It is powered by arXiv API, semantic scholar API, arXiv Vanity, and Adobe PDF API. This project is still in an early stage and I only import the machine learning related papers.
If you want to help me to improve it, visit my GitHub: https://github.com/HMJiangGatech/ArxivRoller