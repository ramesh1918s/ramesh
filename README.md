               To Migrate all Branches from one Git repository Other Git repository
 

Step-by-Step Guide to Migrate All Branches
Clone the source repository

cd Microservice
    
 git remote add target https://github.com/ramesh1918s/ramesh.git

Fetch all branches from the source repository
Fetch all branches from the original repository (it should already be there, but this is a good practice):
            bash
           CopyEdit
     git fetch --all


Push all branches to the target repository
Push all local and remote branches from the source repository to your target repository:
           bash
          CopyEdit
     git push --all target


Push tags (if any)
If the repository contains tags, you can push them as well with:
bash
CopyEdit
git push --tags target


Verify the branches on the target repository
Go to your target GitHub repository (https://github.com/ramesh1918s/ramesh.git) and confirm that all branches have been pushed correctly.
