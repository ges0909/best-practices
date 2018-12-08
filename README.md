# README

## Install

`yarn global add reveal-md`

## Presentation

`reveal-md best-practices.md --theme=moon`

`reveal-md -w best-practices.md --theme=moon --highlight-theme=monokai`

## PDF

`reveal-md best-practices.md --print best-practices.pdf`

## Static

`git init`

`git flow init --default`

`yarn init`

`yarn add reveal-md --save-dev`

`reveal-md best-practices.md --theme=moon --highlight-theme=monokai --static _site`

`yarn reveal-md best-practices.md --theme=moon --highlight-theme=monokai --static _site`

## Pandoc and Reaval.js

`pandoc -t revealjs -s -o best-practices.html best-practices.md -V revealjs-url=https://revealjs.com -V theme=moon -V transistion=cube`
