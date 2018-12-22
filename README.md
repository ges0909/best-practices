# README

Clone repo.

`git clone git@github.com:ges0909/best-practices.git`

`cd best-practices`

Install project dependencies, including `reveal-md`.

`yarn install`

Start live server and present in browser.

`yarn reveal-md best-practices.md --theme=moon --highlight-theme=monokai`

Convert to PDF.

`reveal-md best-practices.md --print best-practices.pdf`

Convert to static site.

`yarn reveal-md best-practices.md --theme=moon --highlight-theme=monokai --static _site`

## Pandoc and Reaval.js

`pandoc -t revealjs -s -o best-practices.html best-practices.md -V revealjs-url=https://revealjs.com -V theme=moon -V transistion=cube`

## Count Source Lines of Code (SLOC)

`yarn global add cloc`

`cloc --exclude-dir=node_modules .`
