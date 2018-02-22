# Code + Learn

Code + Learn is an in-person (and free) event series where developers receive
mentoring from Node.js Core contributors to learn the step-by-step process of
contributing to Node.js Core.

## Purpose

Code & Learn events allow you to get started (or go further) with Node.js core
contributions. Experienced contributors help guide you through your first (or
second or third or fourth) commit to Node.js core. They also are available to
provide impromptu guided tours through specific areas of Node.js core source
code.

## Getting started

- If you don't already have one, [create a GitHub account](https://github.com/join).
- Open the [Node.js core repository web page](https://github.com/nodejs/node).
- Use the "Fork" button to fork the repository:![Image](http://nodetodo.org/getting-started/zfork.png)
- On the page _for your own fork_, use the "Clone or download" button and copy the URL for cloning to your clipboard:![Screenshot](http://nodetodo.org/getting-started/zclone.png)
- On the command line, in your home directory or a directory you have for projects, run:

```console
git clone <paste that URL here>;
```

- When that is done:

```console
cd node
```

- Then:

```console
git remote add upstream https://github.com/nodejs/node.git
```

- Follow [the instructions to build Node.js](https://github.com/nodejs/node/blob/master/BUILDING.md). You do not need to install the version of Node.js built with these instructions. Follow the instructions to build and test only. (You do not need to run the npm tests.)
- If you have not already done so, set git to use the name and email address you would like your commits to be credited to.

```console
git config --global user.name "J. Q. User"
git config --global user.email "j.q.user@example.com"
```

- Look at issues in the Core repository labeled ['good first issue'](https://github.com/nodejs/node/issues?q=is%3Aissue+is%3Aopen+label%3A%22good+first+issue%22) to choose what'd you liked to jump into or visit [#Node.js in IRC](http://webchat.freenode.net/?channels=node.js) to get help with any of the above steps.
- After you have your first pull request open, you can look at the [Next Steps](http://nodetodo.org/next-steps/) page for suggestions on ways to find additional issues to work on.
