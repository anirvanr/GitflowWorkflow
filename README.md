Gitflow Workflow is a Git workflow that helps with continuous software development and implementing DevOps practices.

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

 ### Feature branch process
 
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

`git push origin feature/J-1234`

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
