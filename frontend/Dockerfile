FROM node:16-slim as base
ENV NODE_ENV=production
ENV TINI_VERSION v0.18.0
ENV PORT=3000
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /tini
RUN chmod +x /tini
EXPOSE $PORT
RUN npm i npm@latest -g
RUN mkdir /opt/node_app && chown -R node:node /opt/node_app
WORKDIR /opt/node_app
USER node
COPY --chown=node:node package.json package-lock.json* ./
RUN npm ci && npm cache clean --force


## Stage 2 (development)
# we don't COPY in this stage because for dev you'll bind-mount anyway
# this saves time when building locally for dev via docker-compose
FROM base as dev
ENV NODE_ENV=development
ENV PATH=/opt/node_modules/.bin:$PATH
RUN npm install --only=development
WORKDIR /opt/node_app/app
CMD ["npm", "run", "start"]


## Stage 3 (copy in source)
# This gets our source code into builder for use in next two stages
# It gets its own stage so we don't have to copy twice
# this stage starts from the first one and skips the last two
FROM base as source
WORKDIR /opt/node_app/app
COPY --chown=node:node . .


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