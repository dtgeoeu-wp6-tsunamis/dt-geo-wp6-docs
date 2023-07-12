# Template for static webpage with Mkdocs and Docker

This repository contains a template for [mkdocs](https://squidfunk.github.io/mkdocs-material/). 

## Symbols used in this documentation 

-Variables written in capital letters enclosed with `<>` are variables.

## Local development

Activate the virtual environment and run 

    ~/template-mkdocs-(main)$ poetry shell
    (template-mkdocs-py3.11)~/template-mkdocs(main)$ poetry install
    (template-mkdocs-py3.11)~/template-mkdocs(main)$ mkdocs serve 

This with bring up a mkdocs server on http://localhost:8000/. 

**NB** when you work locally with `mkdocs` you might have to clean you cash memory of the browser, or try to see your new changes in *incognito mode* to be sure. 

## Build docker image locally

Local build of the docker image 

    docker build -t  -mkdocs . 

## Deploy

To deploy a new version tag the respective commit on the main branch with the new version: "v.*.*.*".


