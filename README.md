Gitflow Workflow is a Git workflow that helps with continuous software development and implementing DevOps practices.
<img width="650" src="https://github.com/anirvanr/GitflowWorkflow/blob/master/Screenshot_GitFlow.png">

### Common types of branches

#### Master
Master branch should only store code that that is currently in production. Make Master branch protected, so that no direct commit can be done by anyone against this branch.

#### Develop
The develop branch is a long-lived feature branch that holds changes made by developers before they’re ready to go to production. Don’t make changes directly on develop branch.

#### Feature
Feature branches are used to develop new features for the upcoming or a distant future release. May branch off from develop and must merge into develop.

#### Hotfix
The hotfix branches are reserved for patch production releases. These patches should be done with caution and the responsibility is on the developer and the testing team to ensure that there won't be a negative impact on the production environments. May branch off from master and must merge into master and develop.

#### Release
Release branches support preparation of a new production release. They allow many minor bug to be fixed and preparation of meta-data for a release. May branch off from develop and must merge into master and develop.

The overall flow of Gitflow is:

1.	A `develop` branch is created from `master`
2.	A `release` branch is created from `develop`
3.	`Feature` branches are created from `develop`
4.	When a `feature` is complete it is merged into the `develop` branch
5.	When the `release` branch is done it is merged into `develop` and `master`
6.	If an issue in `master` is detected a `hotfix` branch is created from `master`
7.	Once the `hotfix` is complete it is merged to both `develop` and `master`

 ### Feature scenario
 
- Feature branches are always branched from develop.
- Feature branches need not be deployed.
- Feature branches work best if the branch contains one single commit.
- Feature branches should have a short life.
- Feature branches should represent changes from one and only one developer.
- Frequently rebase the feature branch against the develop branch.

Creating a feature branch:

```bash
git checkout -b feature/J-1234 develop

// Where J-1234 is the Jira ticket this feature corresponds to

// add and commit here
```

If your change contains more work than can be sensibly covered in a single commit though, do not try to squash it down into one. More granular (smaller) commits have many practical benefits. Having a single merge commit containing all the changes to all your files probably isn’t what you want to expose in the end.

```bash
git checkout feature/J-1234
git rev-list --count HEAD

// count the commits for the branch you are on
```

Let say count is “4”
To squash the last 3 commits into one:

```bash
git reset --soft HEAD~3
git commit -m "New message for the combined commit"
```

If you’re working on your `feature/J-1234` branch long enough, you may end up out-of-sync with the develop branch for days or weeks. To keep your feature branch up to date:

```bash
git checkout develop 
git pull
git checkout feature/J-1234
git rebase develop
```

Many times when you do git rebase, you have conflicts. You need to resolve them and continue the next step of the rebase. So first, you go to all files where the conflict is. You resolved them and then you add all your changes using `git add` command. After that, you can continue the rebase using `git rebase --continue` command. It is often a good idea to rebase your branch to tidy up before submitting the PR.

Push feature branch:

```bash
git push origin feature/J-1234
```

- Open a new Pull Request for `feature/J-1234` 
- Have your Pull Request reviewed. Have others make comments if anything needs to change. 
- If changes need to be made, keep the Pull Request open and just do more commits locally, then push to the same branch. 
- Merge the Pull Request once it’s okay. Merging the changes from `feature/J-1234` into `develop` will basically add all the changes that weren’t already there in develop.
- As such, develop just needs to "fast forward" and apply all the changes that were made since `feature/J-1234` was made. 
- If develop branch has been altered since the `feature/J-1234` was checked out, you may have some merge conflicts to fix. These must resolved and committed. 
- Code quality needs to be maintained prior to merging Pull Requests (PRs) into protected branches. 

Once the feature branch has been successfully merged, it can be deleted both locally and on origin as it is no longer required. 

```bash
git checkout develop
git branch -d feature/J-1234
git push origin --delete feature/J-1234
```

### Release scenario

- The release branch is created off the development branch.
- The release branch is merged into master and also back into development.
- Any minor tweaks can be made and committed to this release branch. Remember the rule here is no new functionality. 

```bash
git checkout develop
git pull origin develop
git checkout -b release-1.0.0

// add and commit here
```

Push to a branch on remote with the same name, then make a Pull Request from yours into develop

```bash
git push origin release-1.0.0
```

Once PR approval is received, and go ahead is given to move the release candidate changes to production, as per git flow we merge the `release` branch changes in to the `master` branch and create a tag for the release. We also merge the changes from the `release` branch back in to `develop` branch.

```bash
git checkout develop
git pull origin develop
git merge --no-ff release-1.0.0
git push origin develop
git checkout master
git pull origin master
git merge --no-ff release-1.0.0
git push origin master
```

Finally create a tag on master and delete our release branch

```bash
git checkout master
git pull origin master
git tag -a 1.0.0 -m "xyz feature is released in this tag."
git push origin 1.0.0
git branch -d release-1.0.0
git push origin --delete release-1.0.0
```

### Hotfix scenario

An issue is reported to the support team and a bug is logged.

-	A hotfix branch is created directly off the latest commit on master.
-	The only commits allowed on the hotfix branch are ones that explicitly address the software bug.
-	No feature enhancements or chores are allowed on the hotfix branch.
-	The hotfix branch merges into both master and develop branches when its lifecycle ends.
- The hotfix branch is deleted after it is merged into master and develop branches.

You can list the tags on remote repository with `git ls-remote --tags origin`

```bash
git fetch origin –tags
git checkout -b hotfix-1.0.1 1.0.0

// add and commit here

git push origin hotfix-1.0.1
```

If PR build is successful, and after incorporating any Review comments, the PR changes are merged into the master branch.

```bash
git checkout master
git merge --no-ff hotfix-1.0.1
git tag -a 1.0.1 -m "Hotfix Release 1.0.1"
git push origin master 1.0.1
```

Next, include the hotfix in develop, too:

```bash
git checkout develop
git pull origin develop
git merge --no-ff hotfix-1.0.1
git push origin develop
```

We can also delete the release branch once merged

```bash
git branch -d hotfix-1.0.1
git push origin -d hotfix-1.0.1
```
