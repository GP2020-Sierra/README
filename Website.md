# Website

The website is made in vue.js & nuxt.js which enables us to compile it into a static site. We therefore use one repo with one branch `source` holding the vue source code and another branch `master` to hold the static files to serve (as Github requires using the `master` branch for user or organisation pages).

## To host on Github Pages

1) Fork the https://github.com/GP2020-Sierra/GP2020-Sierra.github.io repo

2) Modify `src/locations.js` to change `gp2020-sierra.azurewebsites.net` to `[YOUR APP FUNCTION NAME].azurewebsites.net`

3) Github actions are used to automatically build the `source` branch into static files on the `master` branch. However, this does not cause Github pages to update so we have to force it.

    - In your Github Account Settings, go to Developer Settings, Personal Access Tokens. Generate a personal access token, selecting the `repo` scope. 

    - In your forked repo, go to Settings, Secrets, Add a new secret called `GH_PAGES_REBUILD_TOKEN` with the value being the personal access token you just added.

4) Enable Github Pages in the repo settings (selecting the `master` branch as the source if asked).

## To host elsewhere

1) Clone the repo `git clone https://github.com/GP2020-Sierra/GP2020-Sierra.github.io`

2) Install Node.js and use npm to install all the packages (`npm install`)

3) Modify `src/locations.js` to change `gp2020-sierra.azurewebsites.net` to `[YOUR APP FUNCTION NAME].azurewebsites.net`

4) Use `npm run generate` to generate the static website files (which will be placed in the `dist` folder)

5) Host the static website files on a webserver of your choice

## To develop

Follow the host elsewhere instruction, but use `npm run dev` to start development mode with quick reloading.