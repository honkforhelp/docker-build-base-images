FROM blakeisrael/heroku-24-ruby-3.4.8

MAINTAINER Blake Israel

# This version also has imagemagick headers (libmagickwand-dev) so gems such as
# rmagick can compile against ImageMagick.

USER root

RUN apt-get update \
 && apt-get install -y --no-install-recommends --fix-missing libmagickwand-dev \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

USER circleci
