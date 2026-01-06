### Git Flow

Git Flow is a branching model for Git, designed around project releases. It encompasses a strict branching model, designed for managing larger projects.

**Main Components:**
- **Master branch:** Always reflects a production-ready state.
- **Develop branch:** Serves as an integration branch for features.
- **Feature branches:** Branch off from develop and merge back into develop.
- **Release branches:** Branch off from develop and merge into develop and master.
- **Hotfix branches:** Branch off from master and merge into develop and master.

**Example Workflow:**
1. Start a new feature:
   ```
   git checkout -b feature/your_feature develop
   ```
2. Finish the feature and merge it into develop:
   ```
   git checkout develop
   git merge --no-ff feature/your_feature
   git branch -d feature/your_feature
   ```
3. Preparing a release:
   ```
   git checkout -b release/1.0.0 develop
   ```
   *Test and finalize the release.*
4. Complete the release:
   ```
   git checkout master
   git merge --no-ff release/1.0.0
   git tag -a 1.0.0
   git checkout develop
   git merge --no-ff release/1.0.0
   git branch -d release/1.0.0
   ```
5. Hotfixes:
   ```
   git checkout -b hotfix/1.0.1 master
   ```
   *After fixing:*
   ```
   git checkout master
   git merge --no-ff hotfix/1.0.1
   git tag -a 1.0.1
   git checkout develop
   git merge --no-ff hotfix/1.0.1
   git branch -d hotfix/1.0.1
   ```

### GitHub Flow

GitHub Flow is a lightweight, branch-based workflow that supports teams and projects where deployments are made regularly.

**Main Components:**
- **Main branch:** Production-ready state at all times.
- **Feature branches:** Branch off from main and should be deployed to production once their PRs are merged.

**Example Workflow:**
1. Create a branch:
   ```
   git checkout -b your_feature
   ```
2. Add commits and push your branch:
   ```
   git push -u origin your_feature
   ```
3. Open a Pull Request (PR) for discussion and review.
4. Deploy from the branch to verify in production.
5. Merge into the main branch.

### Trunk-Based Development

Trunk-Based Development is a version control strategy where developers collaborate on code in a single branch called "trunk", minimizing the existence of long-lived branches.

**Main Components:**
- **Trunk/Main branch:** Single source of truth for the current state of the project.
- **Short-lived feature branches:** Typically exist for less than a day before merged into trunk.
- **Optional release branches:** For teams releasing less frequently.

**Example Workflow:**
1. Developers create short-lived feature branches off the trunk:
   ```
   git checkout -b feature/quick_fix
   ```
2. After testing, the feature is merged back into the trunk:
   ```
   git checkout trunk
   git merge --no-ff feature/quick_fix
   git branch -d feature/quick_fix
   ```
3. Regularly push the trunk changes to the central repository.

In Trunk-Based Development, the focus is on keeping the branches short-lived to encourage continuous integration and minimize merge conflicts.

Next: [Versions](../angular/versions.md)
