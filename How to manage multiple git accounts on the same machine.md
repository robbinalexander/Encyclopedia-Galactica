How to manage multiple git accounts on the same machine

* Delete existing keys

```
$ cd ~/.ssh
$ ssh-add -D
$ rm -rf *
```

* Generate keys for multiple accounts
```
$ ssh-keygen -t rsa -C <personal-email-address>
# Save it as id_rsa_personal
$ ssh-keygen -t rsa -C <work-email-address>
# Save it as id_rsa_work
```

* You should be able to see 2 files for each of your accounts
```
$ ls -la ~/.ssh/
-rw-------   1 ra  staff  1679 Jul  6 18:15 id_rsa_personal
-rw-r--r--   1 ra  staff   408 Jul  6 18:15 id_rsa_personal.pub
-rw-------   1 ra  staff  1675 Jul  6 18:15 id_rsa_work
-rw-r--r--   1 ra  staff   417 Jul  6 18:15 id_rsa_work.pub
```
* Add the generated public keys to each of your git accounts (multiple github, bitbucket, etc. accounts). For eg. for github, navigate to settings -> SSH and GPG keys -> Click on New SSH key -> paste the text of .pub -> Add SSH key
* Create a config file under ~/.ssh/
```
$ vi ~/.ssh/config
#personal
Host personal
    Hostname github.com
    User git
    IdentityFile ~/.ssh/id_rsa_personal
#work
Host work
    Hostname github.com
    User git
    IdentityFile ~/.ssh/id_rsa_work
```

* Add the new identities
```
$ ssh-add id_rsa_personal
Identity added: id_rsa_personal (id_rsa_personal)
$ ssh-add id_rsa_work
Identity added: id_rsa_work (id_rsa_work)
#test using ssh-add -l
```

* Clone a repo
```
git clone git@personal:robbinalexander/blog.git
```

* Commit and push
Git 2.13 introduced conditional includes, which helps you create different user profiles based on different working directories. For eg., you can setup git to use your work email when you are in the work directory (or sub-directories).
```
$ git config --global --edit
[includeif "gitdir:~/work/"]
  path = ~/work/.gitconfig
[includeif "gitdir:~/sandbox/"]
  path = ~/sandbox/.gitconfig
  
$ cat ~/sandbox/.gitconfig
[user]
name=Robbin Alexander
email=<personal-email-address>
```
Remove the default user section from the global git config, and this will allow you to enforce a local user config.
