How to publish packages on NPM and integrate them well with git repos. Follow the sections below in order.

# 1. Setup GitHub

NPM packages are often tied to git repos, so setting up a personal git flow is important. I use GitHub, so this explains how to use GitHub.

0. Setup an account on GitHub: https://github.com
1. Setup ssh keys for GitHub
    - create ssh keys: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent
    - add keys to GitHub: https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account

# 2. Setup Node

0. Install NVM: https://github.com/nvm-sh/nvm#installing-and-updating
1. Install Node (and NPM) with this command: `nvm install --lts`

# 3. Setup NPM

0. Create an account on npm: https://www.npmjs.com
1. Login via the CLI with `npm login`

# 4. Publishing a package

0. Create a new repo in GitHub.
1. Clone the repo to your local file system (use ssh if you followed the above GitHub setup section).
2. Run `npm init -y`.
3. Change anything in the generated `package.json` to your liking.
    - For example, I change the license to `MIT` (you can prevent needing to do this with `npm config set init-license "MIT"`).
4. Run `npm publish`.

Now your package is published! For futher info, you should research how to use `npmignore` files and why they're useful.
