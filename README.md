Phabricator Guide
====
Phabricator is a platform for code review, much like GitHub Pull Requests. Phabricator, however, has a much more extensive and intuitive feature set.

Developer Setup
----
### Add arcanist/bin to $PATH
In your home directory, find ~/.bashrc, open it and append the following path to PATH:

	/mnt/distribute_env/phacility/arcanist/bin

### Install Phabricator certificate
In your project directory (which your PL should have already configured to work with Phabricator properly), run the following:

	arc install-certificate

This should print a URL to your terminal. Follow the link, copy the code given, and paste it back in your terminal.

### Configure default Phabricator URI
Run the following to set a default Phabricator URI:

	arc set-config default http://phabricator.visenze.com

This should enable you to run arc without an .arcconfig for your project.

### Configure local repo git conventions
Navigate to the root of your repo and copy/paste the following into your terminal to curl and run an automated script that will set up your repo with some goodies that should streamline your workflow:

	curl https://raw.githubusercontent.com/flyingbag/phabricator-setup/master/bin/bp-phab-dev-setup.py > bp-phab-dev-setup.py && python bp-phab-dev-setup.py && (yes | rm -f bp-phab-dev-setup.py)

This script does the following automagically:

- Configures a git hook on `pre-commit`, which disables committing to develop.
- Configures a git hook on `pre-push`, which disables pushing to develop.
- Sets up a custom commit message template which defaults to adding your PL and teammates as Reviewer and CCs, respectively.
- Configures a git hook on `prepare-commit-msg` to disable committing with `-m`, in order for git to pick up the aforementioned commit template.
- Sets up your repo to automatically `rebase` on `git pull`.
- Configures `arc diff --verbatim`.

### Voila!
You should be configured to push diffs for code review onto Phabricator, yay! Read the next section to get a feel for the Phabricator workflow.

Developer Workflow
----
Phabricator best practice is to do all your work in branches. You don't no development in the develop branch; that is just a staging branch to push upstream (to GitHub).
Some of these rules are stricly enforced by the above configuration you did on your local repo.

1. When you want to start work on a new feature or change, create a local branch:

		git checkout -b cool-new-feature

2. Make a bunch of changes, and commit them as normal:

		git commit

3. When you're ready for a review, run:

		arc diff

	- **Super useful:** If you'd like to preview your diff before actually submitting it for review, use `arc diff --preview` instead!

4. Often, your PL will give you feedback on your review that will prompt you to make changes before you can push to remote.  Make the changes/fixes as normal in your branch, and then use `git commit --amend` to amend your previous git commit. (This is normally bad GitHub practice, but becomes very useful and actually preferred when using Phabricator.)  Then, run `arc diff`. Your editor should open up with a message indicating that your are making a revision to an _existing_ Phab diff.
	>
	- If you need to force `arc` to assign the new commits to the correct Phab revision ID, use `arc diff --update <revision id>`
	- If you don't need to change your commit message on amend, you can use the `--no-edit` flag to skip editing it.

5. When you are ready to push, run `git push`.

See the Phabricator authors' workflow for more in-depth, detailed info [here](https://secure.phabricator.com/w/guides/arcanist_workflows/).


### Summary
	git checkout -b <feature branch>
	<edit edit edit>
	git commit
	arc diff

	<edit based on review feedback>
	git commit
	arc diff

	<maybe run git pull if you want some updates>
	<edit + commit + arc diff some more>

	git push
