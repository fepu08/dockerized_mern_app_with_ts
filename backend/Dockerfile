FROM node:16-slim as base
# set our node env default to production, compose can override this to development
ENV NODE_ENV=production
ENV TINI_VERSION v0.18.0
ENV PORT=5000
EXPOSE $PORT
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
RUN apt-get update && apt-get install curl -y
RUN npm i npm@latest -g
# because of the default /opt permissions we have to create the node_app directory with root than change the permissions 
RUN mkdir /opt/node_app && chown -R node:node /opt/node_app
WORKDIR /opt/node_app
# the official node image provides an unprivileged user as a security best practice
# but we have to manually enable it. We put it here so npm installs dependencies as the same
# user who runs the app.
# https://github.com/nodejs/docker-node/blob/master/docs/BestPractices.md#non-root-user
USER node
COPY --chown=node:node package.json package-lock.json* ./
RUN npm ci && npm cache clean --force


## Stage 2 (development)
# we don't COPY in this stage because for dev you'll bind-mount anyway
# this saves time when building locally for dev via docker-compose
FROM base as dev
ENV NODE_ENV=development
ENV PATH=/opt/node_modules/.bin:$PATH
RUN npm install --only=development --ignore-scipts 
WORKDIR /opt/node_app/app
CMD ["nodemon", "server.js", "--inspect=0.0.0.0:9229"]


## Stage 3 (copy in source)
# This gets our source code into builder for use in next two stages
# It gets its own stage so we don't have to copy twice
# this stage starts from the first one and skips the last two
FROM base as source
WORKDIR /opt/node_app/app
COPY --chown=node:node ./backend .


## Stage 4 (testing)
# use this in automated CI
FROM source as test
ENV NODE_ENV=development
ENV PATH=/opt/node_modules.bin:$PATH
# this copies all dependencies (prod+dev)
COPY --chown=node:node --from=dev /opt/node_app/node_modules /opt/node_app/node_modules
# run linters as part of build
# be sure they are installed with devDependencies
RUN eslint . 
# run unit tests as part of build
RUN npm test
# run integration testing with docker-compose later
CMD ["npm", "run", "test"] 


## Stage 5 (default, production)
# this will run by default if you don't include a target
# it has prod-only dependencies
# In BuildKit, this is skipped for dev and test stages
FROM source as prod
ENTRYPOINT ["/tini", "--"]
CMD ["node", "server.js"]