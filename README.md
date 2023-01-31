### Create .git directory

```sh
mkdir ./git
```

### Create objects directory

```sh
mkdir ./git/objects
```

### Create refs directory

```sh
mkdir ./git/refs
```

### Point HEAD to the master branch (or 'whatever' branch)

```sh
echo "ref: refs/head/master" > .git/HEAD
```


```
argon@argon:oss/gitplay ‹master*›$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md

nothing added to commit but untracked files present (use "git add" to track)
```

### Create a new blob 

```sh
argon@argon:oss/gitplay ‹master*›$ echo "hello world" | git hash-object --stdin
3b18e512dba79e4c8300dd08aeb37f8e728b8dad
```

### Create a new blob and write the blob into the object database 

```sh
argon@argon:oss/gitplay ‹master*›$ echo "hello world" | git hash-object -w --stdin
3b18e512dba79e4c8300dd08aeb37f8e728b8dad

```

### Verify in .git

```sh

argon@argon:oss/gitplay ‹master*›$ tree .git
.git
├── FETCH_HEAD
├── HEAD
├── objects
│   └── 3b
│       └── 18e512dba79e4c8300dd08aeb37f8e728b8dad
└── refs

```
### Cat the object file type

`-t` - Type

```sh
argon@argon:oss/gitplay ‹master*›$ git cat-file -t 3b18e512dba79e4c8300dd08aeb37f8e728b8dad
blob
```

### Cat the object file 

`-p` - Pretty print

```sh
argon@argon:oss/gitplay ‹master*›$ git cat-file -p 3b18e512dba79e4c8300dd08aeb37f8e728b8dad
hello world

```

#### Git status does not still show any files

```sh
argon@argon:oss/gitplay ‹master*›$ git status                                              
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md

nothing added to commit but untracked files present (use "git add" to track)
```


### Update the index with this new file

```sh
argon@argon:oss/gitplay ‹master*›$ git update-index --add --cacheinfo 100644 3b18e512dba79e4c8300dd08aeb37f8e728b8dad hw.txt 

```

#### Check out git status and see that the file has been added

```
argon@argon:oss/gitplay ‹master*›$ git status                                                                               
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   hw.txt

Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    hw.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md
```

We see the file both in "new file" and "deleted" because we created the object and the index but not the physical file

### Create a physical file with blob contents

```sh
argon@argon:oss/gitplay ‹master*›$ git cat-file -p 3b18e512dba79e4c8300dd08aeb37f8e728b8dad > hw.txt
argon@argon:oss/gitplay ‹master*›$ git status                                                       
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   hw.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md

```

### Create a tree

A commit has reference to a tree. Let's create a tree

```sh
argon@argon:oss/gitplay ‹master*›$ git write-tree 
7cf7564d5a372dd8dcc4a0ba9300ec22ed7f7e5e
```
#### Tree and see the structure

```sh
argon@argon:oss/gitplay ‹master*›$ tree .git
.git
├── FETCH_HEAD
├── HEAD
├── index
├── objects
│   ├── 3b
│   │   └── 18e512dba79e4c8300dd08aeb37f8e728b8dad
│   └── 7c
│       └── f7564d5a372dd8dcc4a0ba9300ec22ed7f7e5e
└── refs

```

#### Cat file to check this new object (tree)

```
argon@argon:oss/gitplay ‹master*›$ git cat-file -t 7cf7564d5a372dd8dcc4a0ba9300ec22ed7f7e5e                                        
tree
argon@argon:oss/gitplay ‹master*›$ git cat-file -p 7cf7564d5a372dd8dcc4a0ba9300ec22ed7f7e5e                                                 
100644 blob 3b18e512dba79e4c8300dd08aeb37f8e728b8dad    hw.txt
```

### Commit the tree to the index (staging area)

```sh
argon@argon:oss/gitplay ‹master*›$ git commit-tree 7cf7564d5a372dd8dcc4a0ba9300ec22ed7f7e5e -m "first commit"
09cc2f691947c0834fcf098381c371c12827a0dd
```

#### `tree .git`

```
argon@argon:oss/gitplay ‹master*›$ tree .git
.git
├── FETCH_HEAD
├── HEAD
├── index
├── objects
│   ├── 09
│   │   └── cc2f691947c0834fcf098381c371c12827a0dd
│   ├── 3b
│   │   └── 18e512dba79e4c8300dd08aeb37f8e728b8dad
│   └── 7c
│       └── f7564d5a372dd8dcc4a0ba9300ec22ed7f7e5e
└── refs
```

#### Cat file to check the new object (commit)

```sh
argon@argon:oss/gitplay ‹master*›$ git cat-file -t 09cc2f691947c0834fcf098381c371c12827a0dd                                        
commit
argon@argon:oss/gitplay ‹master*›$ git cat-file -p 09cc2f691947c0834fcf098381c371c12827a0dd
tree 7cf7564d5a372dd8dcc4a0ba9300ec22ed7f7e5e
author Arun Manivannan <arun@arunma.com> 1675146888 +0800
committer Arun Manivannan <arun@arunma.com> 1675146888 +0800

first commit
```

### Summary

Commit `09` points to tree `7c`
Tree `07` points to Blob `3b`


#### Unfortunately the `git status` does not reflect this

```sh
argon@argon:oss/gitplay ‹master*›$ git status                                              
On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
        new file:   hw.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md
```

### Reflection

`HEAD` is just a pointer to the branch (one of the `heads`). 
`heads` must point to a `commit`

### Create a new branch 

A branch is nothing but a named file inside the `.git/refs/heads` directory.

```sh
argon@argon:oss/gitplay ‹master*›$ mkdir -p .git/refs/heads                                                
argon@argon:oss/gitplay ‹master*›$ echo "09cc2f691947c0834fcf098381c371c12827a0dd" > .git/refs/heads/master
```

#### Git status

The `hw.txt` is not staged anymore. It is committed.

```sh
argon@argon:oss/gitplay ‹master*›$ git status
On branch master
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md

nothing added to commit but untracked files present (use "git add" to track)
```

### Create a new branch

Again, a named file in `.git/refs/heads/test`. 

Let's point the same commit from this new branch. 

```sh
argon@argon:oss/gitplay ‹master*›$ echo "09cc2f691947c0834fcf098381c371c12827a0dd" > .git/refs/heads/test  
```

#### Git log

Both `master` and `test` point to the same commit hash. 

```sh
commit 09cc2f691947c0834fcf098381c371c12827a0dd (HEAD -> master, test)
Author: Arun Manivannan <arun@arunma.com>
Date:   Tue Jan 31 14:34:48 2023 +0800

    first commit
```

### Switch branch from `master` to `test`

```sh

argon@argon:oss/gitplay ‹master*›$ echo "ref: refs/heads/test" > .git/HEAD                         
argon@argon:oss/gitplay ‹test*›$ git status
On branch test
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md

nothing added to commit but untracked files present (use "git add" to track)

```
### Create a new file

```sh
argon@argon:oss/gitplay ‹test*›$ echo "new file" | git hash-object -w --stdin
fa49b077972391ad58037050f2a75f74e3671e92
argon@argon:oss/gitplay ‹test*›$ git update-index --add --cacheinfo 100644 fa49b077972391ad58037050f2a75f74e3671e92 new.txt  
argon@argon:oss/gitplay ‹test*›$ git cat-file -p fa49b077972391ad58037050f2a75f74e3671e92                                        
new file
argon@argon:oss/gitplay ‹test*›$ git write-tree                                          
b57ada7fde872f65a3b9f28995d945923f6ae1a1
argon@argon:oss/gitplay ‹test*›$ git commit-tree b57ada7fde872f65a3b9f28995d945923f6ae1a1 -m "test commit"                                         
627b929dd8453f13d68e511a3477e4aa65c33041
argon@argon:oss/gitplay ‹test*›$ echo "627b929dd8453f13d68e511a3477e4aa65c33041" > .git/refs/heads/test 
argon@argon:oss/gitplay ‹test*›$ git status                                                               
On branch test
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        deleted:    new.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md

no changes added to commit (use "git add" and/or "git commit -a")
argon@argon:oss/gitplay ‹test*›$ git cat-file -p fa49b077972391ad58037050f2a75f74e3671e92 > new.txt                              
argon@argon:oss/gitplay ‹test*›$ git status                                                        
On branch test
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md

nothing added to commit but untracked files present (use "git add" to track)
```

### Git log

```sh
commit 627b929dd8453f13d68e511a3477e4aa65c33041 (HEAD -> test)
Author: Arun Manivannan <arun@arunma.com>
Date:   Tue Jan 31 14:54:41 2023 +0800

    test commit
```

### Compare branches 

```sh
argon@argon:oss/gitplay ‹test*›$ cat .git/HEAD                                           
ref: refs/heads/test
argon@argon:oss/gitplay ‹test*›$ cat .git/refs/heads/test
627b929dd8453f13d68e511a3477e4aa65c33041
argon@argon:oss/gitplay ‹test*›$ git cat-file -p 627b929dd8453f13d68e511a3477e4aa65c33041
tree b57ada7fde872f65a3b9f28995d945923f6ae1a1
author Arun Manivannan <arun@arunma.com> 1675148081 +0800
committer Arun Manivannan <arun@arunma.com> 1675148081 +0800

test commit
argon@argon:oss/gitplay ‹test*›$ git cat-file -p b57ada7fde872f65a3b9f28995d945923f6ae1a1                                        
100644 blob 3b18e512dba79e4c8300dd08aeb37f8e728b8dad    hw.txt
100644 blob fa49b077972391ad58037050f2a75f74e3671e92    new.txt
 
----------------------

argon@argon:oss/gitplay ‹test*›$ cat .git/refs/heads/master 
09cc2f691947c0834fcf098381c371c12827a0dd
argon@argon:oss/gitplay ‹test*›$ git cat-file -p 09cc2f691947c0834fcf098381c371c12827a0dd
tree 7cf7564d5a372dd8dcc4a0ba9300ec22ed7f7e5e
author Arun Manivannan <arun@arunma.com> 1675146888 +0800
committer Arun Manivannan <arun@arunma.com> 1675146888 +0800

first commit
argon@argon:oss/gitplay ‹test*›$ git cat-file -p 7cf7564d5a372dd8dcc4a0ba9300ec22ed7f7e5e
100644 blob 3b18e512dba79e4c8300dd08aeb37f8e728b8dad    hw.txt
```

Notice that both `hw.txt` and `new.txt` are blobs


### Brand new branch with commit tree inheritance

```
argon@argon:oss/gitplay ‹foobar*›$ echo "foo" | git hash-object -w --stdin                 
257cc5642cb1a054f08cc83f2d943e56fd3ebe99
argon@argon:oss/gitplay ‹foobar*›$ git cat-file -p 257cc5642cb1a054f08cc83f2d943e56fd3ebe99                                        
foo
argon@argon:oss/gitplay ‹foobar*›$ git cat-file -p 257cc5642cb1a054f08cc83f2d943e56fd3ebe99 > foo.txt
argon@argon:oss/gitplay ‹foobar*›$ gst                                                               
On branch foobar
Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md
        foo.txt

nothing added to commit but untracked files present (use "git add" to track)
argon@argon:oss/gitplay ‹foobar*›$ git update-index --add --cacheinfo 100644 257cc5642cb1a054f08cc83f2d943e56fd3ebe99 foo.txt
argon@argon:oss/gitplay ‹foobar*›$ git write-tree                                                                            
6bc243aeb80f08ab2b42d7bd067059cce86283b6
argon@argon:oss/gitplay ‹foobar*›$ git commit-tree 6bc243aeb80f08ab2b42d7bd067059cce86283b6 -p 09cc2f691947c0834fcf098381c371c12827a0dd -m "foo branch first commit" 
9a8e2079c3386d3ab7b73d948a93e6244eebbcd9
argon@argon:oss/gitplay ‹foobar*›$ gst
On branch foobar
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   foo.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md

argon@argon:oss/gitplay ‹foobar*›$ git cat-file -p 9a8e2079c3386d3ab7b73d948a93e6244eebbcd9                                                  
tree 6bc243aeb80f08ab2b42d7bd067059cce86283b6
parent 09cc2f691947c0834fcf098381c371c12827a0dd
author Arun Manivannan <arun@arunma.com> 1675154747 +0800
committer Arun Manivannan <arun@arunma.com> 1675154747 +0800

foo branch first commit
argon@argon:oss/gitplay ‹foobar*›$ echo "9a8e2079c3386d3ab7b73d948a93e6244eebbcd9" > .git/refs/heads/foo          
argon@argon:oss/gitplay ‹foobar*›$ gst                                                     
On branch foobar
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        new file:   foo.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)
        README.md

argon@argon:oss/gitplay ‹foobar*›$ echo "ref: refs/heads/foo" > .git/HEAD                            
argon@argon:oss/gitplay ‹foo*›$ git log

commit 9a8e2079c3386d3ab7b73d948a93e6244eebbcd9 (HEAD -> foo)
Author: Arun Manivannan <arun@arunma.com>
Date:   Tue Jan 31 16:45:47 2023 +0800

    foo branch first commit

commit 09cc2f691947c0834fcf098381c371c12827a0dd (master)
Author: Arun Manivannan <arun@arunma.com>
Date:   Tue Jan 31 14:34:48 2023 +0800

    first commit

```

### Summary

1. `git log` goes to `HEAD`
1. `HEAD` points to one of the `./git/refs/heads` aka "branch"
1. `heads` are just named files that has pointers to a `commit` tree
1. `commit` tree points to a `tree`
1. `tree` points to sub `tree` or `blob` objects
1. `blob` stores the hashes of the files



