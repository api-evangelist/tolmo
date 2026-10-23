---
title: "Anatomy of a Malicious Package: The Install-Time Playbook"
url: "https://tolmo.com/blog/anatomy-of-a-malicious-package/"
date: "2026-06-11"
feed_url: "https://tolmo.com/blog/index.xml"
---
Installing a dependency runs code. That one fact is the whole attack surface of the software supply chain. A postinstall hook, a setup.py , a build.rs , or a .pth file gives npm install or pip install a place to execute whatever the author wrote, on your laptop or your CI runner, before you have read a line of it.
