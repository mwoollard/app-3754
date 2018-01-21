#  CocoaPods Issue [7354](https://github.com/CocoaPods/CocoaPods/issues/7354)
To reproduce this issue with 1.4.0:

```
$ pod --version
1.4.0
$ git clone https://github.com/mwoollard/app-7354.git
$ cd app-7354
$ pod install
Analyzing dependencies
Downloading dependencies
Installing Bits (1.0.0)
Installing Core (2.0.1)
Installing Debugging (1.0.0)
Generating Pods project
Integrating client project

[!] Please close any current Xcode sessions and use `App7354.xcworkspace` for this project from now on.
Sending stats
Pod installation complete! There is 1 dependency from the Podfile and 3 total pods installed.
```
Pod **Core** from the master repo has been integrated even though there is a matching pod **Core** available in the private repo specified first in the Podfile.

Now edit the Podfile so:

```
pod 'Core'
```
becomes:

```
pod 'Core', '0.1'
```
then:

```
$ pod update
Update all pods
Updating local specs repositories
$ /usr/bin/git -C /Users/markw/.cocoapods/repos/issue-7354 fetch origin --progress
$ /usr/bin/git -C /Users/markw/.cocoapods/repos/issue-7354 rev-parse --abbrev-ref HEAD
master
$ /usr/bin/git -C /Users/markw/.cocoapods/repos/issue-7354 reset --hard origin/master
HEAD is now at a5f04b9 [Add] Core (0.1)
$ /usr/bin/git -C /Users/markw/.cocoapods/repos/master fetch origin --progress
remote: Counting objects: 9, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 9 (delta 6), reused 9 (delta 6), pack-reused 0
From https://github.com/CocoaPods/Specs
00ee862dcfe..c6f65b2f446  master     -> origin/master
$ /usr/bin/git -C /Users/markw/.cocoapods/repos/master rev-parse --abbrev-ref HEAD
master
$ /usr/bin/git -C /Users/markw/.cocoapods/repos/master reset --hard origin/master
HEAD is now at c6f65b2f446 [Add] SJVideoPlayer 1.4.0
Analyzing dependencies
Removing Bits
Removing Debugging
Downloading dependencies
Installing Core 0.1 (was 2.0.1)
Generating Pods project
Integrating client project
Sending stats
Pod installation complete! There is 1 dependency from the Podfile and 1 total pod installed.
```
This time **Core** from the private repo is integrated. Prior to 1.4.0 (including 1.4.0rc1) **Core** from the private repo would be integrated without the version specification in the Podfile. This earlier behaviour I believe is correct.
