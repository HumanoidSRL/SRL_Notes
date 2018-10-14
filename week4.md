# Week 4
## 计划
### 劳天成
下周看完机器人运动学的两份PDF；
并且开始看QT上的代码是什么意思。
### 周虹宇
### 李博&王斌
## 工作记录
### 劳天成
这周主要是继续学了git的相关知识，大概把教程里的都看完了，觉得还挺有意思。
以下是这周学git写的笔记，贴一下，请大家自动忽略英文拼写错误（如果扫了一眼的话）。
git reset --hard commit_id   // to go the id presenting edtion 
HEAD HEAD^                   //all these means the edtion   such as HEAD~100 for the edtion long time ago 
git checkout -- file          //if not placed to the stage  that means the last edition that is commited
                              //if place to the stage  that means the change after being placed to the stage is cleared
git reset HEAD file        //if you change and place to the stage  this will help you to unload it from the stage
git remote add origin git@server-name:path/repo-name.git  //servername here is github.com   path is your name  mine is Ltcccmy reposite name is
                                                           the one you build on the github  github is really a good server-name
git clone git@github.com:Ltcccmy/learngit.git            //a way to clone from the reposity online but only see the master
git push origin master                      //to push to origin(the connected online git reposity) on the master branch 
git checkout -b dec                         //creat a new branch called dev and switch to it
git branch                                  //take a look at the branches
git checkout dec                            //turn to the dec branch
git branch dev                           //creat a new branch called dev
git merge dev                                //get the dev branch involved
git branch -d dev                           //delete the dev branch
git log --graph --pretty=oneline --abbrev-commit // take a look at the branch-commit history
git merge --no-ff -m "merge with no-ff" dev              //to merge in a style that will save the branch history
git branch -D feature-vulcan                              //use this to delete the branch that has not been merged
git checkout -b dev origin/dev                            //the local dev is created to fix the problem that if you clone, you can only see master
git branch --set-upstream-to=origin/dev dev                // to link the local dev to the online dev
git pull                                                  // get the exist edition of the txt from online and check and fix it 
git remote (-v)                                            // add a -v you can see more information on the online reposity   without -v can only see the name of the online reposity
git rebase                                                // turn the histoty line to be a direct line but it change the commit info; it can only make sure that the latest commit is right, the content will not change
git tag <name>     git tag v0.9 f52c633        git tag -a v0.1 -m "version 0.1 released" 1094adb                               // it is to give a commit a tag to help us find it easily   if without an id it will be for the latest commit
git tag                                                    //to see all the tags
git show <tagname>                                           //it will show the info of the tag such as date version and so on
git push origin <tagname>                                    //push a tag  but tags are not necessary to push
git push origin --tags                                        //push all unpushed tags in one time
git tag -d <tagname>                                           //the tag is deleted in the local
git push origin :refs/tags/<tagname>                          //first delete the tag locally then use this line to delete it online
git config --global color.ui true                              //let git show the color but actually in my windows this is a default choice
write a .gitignore to list the txt or .py so on to be ignored  and the .gitignore itself should not be ignored and should be included in the edition manager system  for those needed or will be put in the git file but not be pushed or commited like key
git check-ignore -v App.class                                   //to check why i can not add App.class
git add -f App.class                                            //force to add App.class
git config --global alias.st status                             //give status another name ——st
git config --global alias.unstage 'reset HEAD'                  //unstage to stand for the 'reset HEAD' which means unstage from the stage
git config --global alias.last 'log -1'                          //last means 'log -1' which means the last commit info
cat .git/config                                                  //cat is to show the whole content of the file .git/config is a place keep the current reposity info
cat .gitconfig                                                   //to the just place where the file exist to show the file's content 
about github: use fork to copy others' work   use pull request to let him see and decide whether to accept your change

以及开始看机器人运动学的PDF，坐标变换部分和现在理论力学部分讲的差不多。
### 周虹宇
### 李博&王斌
## 组会报告（郝鸣）
