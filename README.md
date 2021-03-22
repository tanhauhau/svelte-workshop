# Svelte from Zero to Hero

A step-by-step workshop to build a Svelte application, all while learning Svelte fundamentals.

## Pre-Workshop Instructions

In order to maximize our time _during_ the workshop, please complete the following tasks in advance:

- [ ] Set up the project (follow the [setup instructions](#system-requirements) below)
- [ ] Install and run [Zoom](https://zoom.us/) on the computer you'll be developing with
- [ ] Set up dual monitors for live coding, if possible
- [ ] Install a code editor, such as [Visual Studio Code](https://code.visualstudio.com/)
- [ ] Brush up on [modern ES.next features](https://javascript.info), if they are unfamiliar to you
- [ ] Have experience building websites with HTML, CSS, and JavaScript DOM APIs

The more prepared you are for the workshop, the better it will go for you! 👍

## System Requirements

- [git](https://git-scm.com/) v2 or higher
- [Node.js](https://nodejs.org/en/) v14 or higher
- [npm](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm) v6 or higher

All of these must also be available in your `PATH` in order to be run globally. To verify things are set up properly, run:

```sh
git --version
node --version
npm --version
```

If you have a lower version of node installed, and do not wish to upgrade your node version, you can [install `nvm`](https://github.com/creationix/nvm#install-script) to manage multiple versions of node.

If you have trouble with any of these, learn more about the `PATH` environment variable and how to fix it here for [Windows](https://www.howtogeek.com/118594/how-to-edit-your-system-path-for-easy-command-line-access/) or [Mac/Linux](http://stackoverflow.com/a/24322978/971592).

## Setup

After you have verified that you have the proper tools installed (and at the proper versions), getting setup _should_ be a breeze. Run the following commands:

```sh
git clone https://github.com/tanhauhau/svelte-workshop.git
cd react-workshop
npm run setup
```

This will likely take a **few minutes** to run. It will clone the repo, install all of the JavaScript dependencies needed to build our app, and setup our workshop dev directory.

If it fails, please read through the error logs and see if you can figure out what the problem is. Double check that you have the proper [system requirements](#system-requirements) installed. If you are unable to figure out the problem on your own, please feel free to [file an issue](https://github.com/tanhauhau/svelte-workshop/issues/new) with _everything_ (and I mean everything) from the output of the commands you ran.

