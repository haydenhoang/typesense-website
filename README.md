# Typesense Website

This repository contains Typesense landing pages [typesense.org](https://typesense.org) and documentation.

We use VuePress for a framework for `/docs` and use Nuxt for all other routes.

- The Vuepress docs site is under `docs-site`
- The rest of the Nuxt site is under `typesense.org`

We do this because Vuepress is specifically optimized (& opinionated) for documentation sites, whereas Nuxt is a generic and flexible Vue framework.
We need this flexibility for the non-documentation parts of typesense.org.

## Deploying

To deploy both sites together run:

```shell
bash deploy.sh
```

See README.md of each site for more site-specific information. 

