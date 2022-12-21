# Code of conduct

What to do when starting a sprint:
1.  Create user stories in backlog (if needed)
2.  Add acceptance criteria
3.  Do planning poker
4.  Add child issues (tasks)
5.  Move user stories to a sprint and start the sprint
6.  Assign everyone to tasks

What to do when first working on a task:
-   Create a branch from develop
-   Put the Jira id of the task at the start of the branch name, and then describe it very briefly
-   Example:
-   Task on Jira: MOD-19 – Create the menu page in the front-end
-   Branch name: MOD-19_create-menu-page
-   If multiple people are on a task branch, always make sure you have pulled everything before pushing.

At the start of every day, a stand up is performed.

Definition of done:
1.  Before a merge request is started, make sure you have tested your code and have high code coverage. Also make sure you have no errors and have no comment or test code, unless you have a good reason for pushing it to the develop branch.
2.  Start a merge request to the develop branch and assign a person to the merge request. Also put the task ticket in Jira to ‘to review’.
3.  Once the merge request is approved, the assigned person will merge the branch into the develop branch and start the CI/CD pipeline.
4.  Once the pipeline has succeeded, the assigned person will move the task ticket in Jira from ‘to review’ to ‘done’. The assigned person will also update the status of the acceptance criteria in the user story that task belonged to.
5.  If all the acceptance criteria in a user story are done and all the tasks are finished, move the user story ticket to ‘done’.

What to do when ending a sprint:
-   Do a sprint review with the product owners.
-   Do a sprint retrospective.
-   Each sprint retrospective has a scrum board.