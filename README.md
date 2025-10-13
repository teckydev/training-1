# training-1
ssue Summary

You were trying to commit your EMS-angular project, which has two main folders:

EMS-frontend/  (Angular frontend)
server/        (Node.js backend)


But:

Only the server folder was committing, and the frontend was not appearing on GitHub.

Attempts to git add EMS-frontend failed with errors like:

fatal: Pathspec 'EMS-frontend/src' is in submodule 'EMS-frontend'


Even running git commit said:

nothing to commit, working tree clean

Root Cause

At some point, EMS-frontend had its own .git folder, or was added as a submodule, so Git was treating it as a nested Git repository (submodule) instead of normal files.

Because of this, the main repository only tracked a submodule pointer (gitlink), not the actual frontend files.

Even though your source files existed on disk (src/, angular.json, etc.), Git refused to commit them because submodules are tracked differently.

How We Fixed It

Removed the submodule pointer from the main repo:

git rm --cached EMS-frontend


This tells Git to stop treating EMS-frontend as a submodule. Your files stay intact.

Confirmed there was no nested .git inside EMS-frontend

dir EMS-frontend /a


If .git existed, it would be deleted (rmdir /s /q EMS-frontend\.git).

In your case, .git didn’t exist, so this step was skipped.

Committed the removal of the submodule reference:

git commit -m "Remove EMS-frontend submodule reference"


Added EMS-frontend and server as normal folders:

git add EMS-frontend
git add server


Committed the frontend + backend files:

git commit -m "Add EMS-frontend source files and server folder to main repo"


Pushed to GitHub:

git push origin main

Result

EMS-frontend is now tracked as a normal folder, not a submodule.

All frontend source files (src/, angular.json, package.json, etc.) are now on GitHub.

server folder is tracked as before.

Future commits of frontend + backend work normally.

No code was lost at any step.

✅ Key Lesson

If Git says “in submodule” or “nothing to commit” even though files exist, check if the folder has its own .git folder or is a submodule pointer.

Removing the submodule reference (git rm --cached <folder>) is the safe way to make Git track it as a normal folder.
//command 
git rm --cached EMS-frontend
git commit -m "Remove EMS-frontend submodule reference"
>git add EMS-frontend
>git push origin main
