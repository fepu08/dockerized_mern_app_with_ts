# dockerized_mern_app_template

This is a template repo for dockerized (for development not for deployment) MERN apps

# commitlint & githooks

## Prepare

In order to be able to enjoy the benefits of `commitlint` you need to run `npm install --prefix ./backend`

## Alternative Solution

I haven't tested yet, but probably if you create a `package.json` inside a root directory and install `husky` and `commitlint` you can do the same as me

## Disable githooks

If you don't want to use these hooks just run `git config --unset core.hooksPath` or `git config core.hooksPath .git/hooks`

# Mentions

## Docker

The docker and docker-compose files was designed to be able to run and develop this project without install `node` and `npm` on your local machine, however **without install the dependencies, linting and typescript won't work well together.**

For the Docker files I used _Bret Fisher_'s Udemy courses and github repositories:

- https://github.com/BretFisher/udemy-docker-mastery
- https://github.com/BretFisher/docker-mastery-for-nodejs

Both courses are amazing :)
