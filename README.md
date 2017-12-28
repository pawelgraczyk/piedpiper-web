# piedpiper-web

[![Build Status](https://travis-ci.org/agconti/piedpiper-web.svg?branch=master)](https://travis-ci.org/agconti/piedpiper-web)
[![Built with](https://img.shields.io/badge/Built_with-Cookiecutter_Django_Rest-F7B633.svg)](https://github.com/agconti/cookiecutter-django-rest)

Its all about a Weissman score > 5.0. Check out the project's [documentation](http://agconti.github.io/piedpiper-web/).

# Prerequisites

- [Docker](https://docs.docker.com/docker-for-mac/install/)  
- [Travis CLI](http://blog.travis-ci.com/2013-01-14-new-client/)
- [Heroku Toolbelt](https://toolbelt.heroku.com/)

# Initialize the project

Create a superuser to login to the admin:

```bash
docker-compose run --rm web ./manage.py createsuperuser
```

Start the dev server for local development:
```bash
docker-compose up
```

# Continuous Deployment

Deployment is automated via Travis. When builds pass on the master or qa branch, Travis will deploy that branch to Heroku. Enable this by:

Initializing the production sever:

```
heroku create piedpiper-prod --remote prod && \
    heroku addons:create newrelic:wayne --app piedpiper-prod && \
    heroku addons:create heroku-postgresql:hobby-dev --app piedpiper-prod && \
    heroku config:set DJANGO_SECRET=`openssl rand -base64 32` \
        DJANGO_AWS_ACCESS_KEY_ID="Add your id" \
        DJANGO_AWS_SECRET_ACCESS_KEY="Add your key" \
        DJANGO_AWS_STORAGE_BUCKET_NAME="piedpiper-prod" \
        --app piedpiper-prod
```

Initializing the qa sever:

```
heroku create `piedpiper-qa --remote qa && \
    heroku addons:create newrelic:wayne && \
    heroku addons:create heroku-postgresql:hobby-dev && \
    heroku config:set DJANGO_SECRET=`openssl rand -base64 32` \
        DJANGO_AWS_ACCESS_KEY_ID="Add your id" \
        DJANGO_AWS_SECRET_ACCESS_KEY="Add your key" \
        DJANGO_AWS_STORAGE_BUCKET_NAME="piedpiper-qa" \
```

Securely adding your Heroku credentials to Travis so it can automatically deploy your changes.

```bash
travis encrypt HEROKU_AUTH_TOKEN="$(heroku auth:token)" --add
```

Committing your changes and pushing to master and qa to trigger your first deploys:

```bash
git commit -a -m "ci(travis): added heroku credentials" && \
git push origin master:qa && \
git push origin master
```
You're now ready to continuously ship! ✨ 💅 🛳
