# basics

## Git Flow

Git Flow is a popular branching model and version control management approach for Git, developed by Vincent Driessen in 2010. It provides a structured way to manage a project’s development process and organizes how branches are created, merged, and released. Git Flow introduces clear roles for different types of branches, making it easier to manage releases, hotfixes, and ongoing development in a collaborative environment.

Here’s a breakdown of the Git Flow approach:
*Key Concepts in Git Flow:*

Git Flow organizes the repository with five primary types of branches:

1. Main (or Master) Branch (main or master)
   This is the production-ready branch. Every commit on the main branch should represent a stable, production-ready state. Only release or hotfix branches are merged into main. 

2. Development Branch (develop) 
   The develop branch contains the latest development changes. It is used as the integration branch for features and other development work. Features are merged into develop, and when develop is stable, it gets merged into main for a new release. develop should always contain the latest, unstable code that is ready for testing or future release. 

3. Features Branches (feature/)
   Feature branches are used for developing new features or significant changes in the codebase. These branches are created off of develop and should be focused on one specific feature or task. Once the feature is complete, the branch is merged back into develop. Example: feature/login-system, feature/payment-gateway

4. Release Branches (release/)
   Release branches are created from develop when the code is stable enough to be prepared for production. These branches allow for final bug fixes, minor improvements, and documentation updates without disturbing ongoing feature development. Once a release branch is stable, it is merged into both main (to mark the release) and develop (to include any fixes made during the release process). Example: release/1.0.0

5. Hotfix Branches (hotfix/)
   Hotfix branches are created from main when a critical bug or issue is discovered in the production code. These branches allow you to patch the issue quickly, without waiting for the next release cycle. Once the hotfix is complete, it is merged into both main (for the production update) and develop (to include the fix in ongoing development). Example: hotfix/fix-login-bug

### Git Flow Workflow

1. Starting a new feature
   Create a new feature branch from `develop`:
   git checkout develop
   git checkout -b feature/my-new-feature

   Develop the feature and commit changes as usual.
   When the feature is complete, merge it back into develop:
   git checkout develop
   git merge feature/my-new-feature

2. Preparing a Release
   When develop is ready for a release, create a release branch from develop:
   git checkout develop
   git checkout -b release/1.0.0

   In this branch, perform any necessary fixes or adjustments (bug fixes, documentation, etc.).
   Once everything is ready, merge the release branch into both main and develop:
   git checkout main
   git merge release/1.0.0
   git tag -a 1.0.0
   git checkout develop
   git merge release/1.0.0

3. Handling a Hotfix
   When a critical bug is found in production, create a hotfix branch from main:
   git checkout main
   git checkout -b hotfix/fix-issue

   Fix the bug, commit the changes, and merge the hotfix back into both main and develop:
   git checkout main
   git merge hotfix/fix-issue
   git tag -a 1.0.1
   git checkout develop
   git merge hotfix/fix-issue

4. Finishing a Feature or Release
   When a feature or release is complete, it is merged into the appropriate branch (usually develop for features, main for releases).
   Tag releases in main for versioning and tracking.
   If there are no conflicts, the merge is simple, and the branch can be deleted after merging.


Note: Alternates to Git Flow
- Github Flow
- GitLab Flow

