---
title: git命令
tags:
  - shell
  - 版本控制
  - code
date: 2017-06-16 18:02:11
---

```
git clone git@github.com-personal:H***/ffmpeg.git
cd ffmpeg/
git remote -v
git remote add upstream git://source.ffmpeg.org/ffmpeg.git
git remote -v

git fetch upstream
git checkout master
git merge upstream/master

git checkout -b dev
git push --set-upstream origin dev
```
<!--more-->

[https://gist.github.com/jexchan/2351996](https://gist.github.com/jexchan/2351996)

安装git-lfs：[https://github.com/git-lfs/git-lfs/wiki/Installation](https://github.com/git-lfs/git-lfs/wiki/Installation)

1.  1.  Install git &gt;= 1.8.2

            *   Recommended method for RHEL/CentOS 5 and 7 (not 6!)

                    1.  Install the epel repo [link](https://fedoraproject.org/wiki/EPEL#How_can_I_use_these_extra_packages.3F) (For CentOS it's just `sudo yum install epel-release`)
            2.  `sudo yum install git`

                *   Recommended method for RHEL/CentOS 6

                    1.  Install the IUS Community repo. `curl -s https://setup.ius.io/ | sudo bash` or [here](https://ius.io/GettingStarted/)
            2.  `sudo yum install git2u`

                *   You can also build git from source and install it. If you do that, you will need to either manually download the the git-lfs rpm and install it with `rpm -i --nodeps git-lfs*.rpm`, or just use the [Other](https://github.com/git-lfs/git-lfs/wiki/Installation#Other) instructions. The only other advanced way to fool yum is to create and install a fake/real git rpm to satisfy the git &gt;= 1.8.2 requirement.

        2.  To install the git-lfs repo, run `curl -s https://packagecloud.io/install/repositories/github/git-lfs/script.rpm.sh | sudo bash`from [here](https://packagecloud.io/github/git-lfs/install)
    3.  `sudo yum install git-lfs`
    4.  `git lfs install`
推送大文件到github: [https://medium.com/@AyunasCode/how-to-push-large-files-to-github-253d05cc6a09](https://medium.com/@AyunasCode/how-to-push-large-files-to-github-253d05cc6a09)

**So how do we push large files like audios, videos, datasets, and graphics to GitHub?**

Here’s what I did. (I hope my learning experience helps other developers who bump into the same issue).

1.  Follow directions in your push commit error and go to [git-lfs.github.com](https://git-lfs.github.com/). Download Git extension for versioning large files (Git LFS) or install it via Homebrew. [Instructions](https://git-lfs.github.com/). **NB**: **Remove** your large file from your local repo directory **BEFORE** you set tracking for Git LFS.
2.  Here’s the tricky part: you probably already added and removed your large file a couple of times and tried to push to GitHub. I certainly did. Now, your/my commits still have that large file data tracked locally that hasn’t been pushed.
3.  You may need to squash your commits or [checkout](https://www.atlassian.com/git/tutorials/undoing-changes/) changes, but this is the hack I came up with: when you remove your large file from your local repo, reset to a commit that was right BEFORE you added the file.
4.  git log and git reset commands will help get back to that commit.
Example:
<figure id="4dda" class="graf graf--figure graf-after--li">
<div class="aspectRatioPlaceholder is-locked">
<div class="aspectRatioPlaceholder-fill"></div>
<div class="progressiveMedia js-progressiveMedia graf-image is-canvasLoaded is-imageLoaded" data-image-id="1*TqqrwJ--nzUjDdAM1Y6Vzw.png" data-width="874" data-height="217" data-action="zoom" data-action-value="1*TqqrwJ--nzUjDdAM1Y6Vzw.png" data-scroll="native"><canvas class="progressiveMedia-canvas js-progressiveMedia-canvas" width="75" height="17"></canvas>![](https://cdn-images-1.medium.com/max/800/1*TqqrwJ--nzUjDdAM1Y6Vzw.png)</div>
</div></figure>

5\. Set up Git LFS tracking for the type of files you want to add to your repo. [Instructions](https://git-lfs.github.com/). Example:

<pre id="9fe3" class="graf graf--pre graf-after--p">git lfs track "*.psd" // for photoshop files
git lfs track "*.gif" // for gif video files</pre>

6\. Add the file to your repo. LFS will now start tracking it and thus allow the push commit. Then add, commit, and push as usual. Example:

<pre id="7d24" class="graf graf--pre graf-after--p">git add FoodbLogAppDemo.gif
git commit -m "Add video demo"
git push origin master</pre>

Note: after the data is finished uploading, you might see the same error (the file is too large) in your terminal. Check your repo on GitHub’s website and refresh the browser. Your file should be uploaded, despite the error message.

So, once again:

**FIRST **add git lfs track, **THEN** add a large file (like audio, etc.) to your repo.
