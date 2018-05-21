## Simple Git Deployment
### Part of the issue Git Your Act and Deployment Together.

If you’re still using FTP to deploy websites, knock it off. Here’s a simple way to use git to deploy web sites and applications.

You have three repositories: remote, local, and web server. Push from local to remote, clone the repository on your web server, and then simply pull updates after you push.
Local Machine: Push your local repository to a remote server. Check out git for beginners if you’re lost already.

```git
 $ git remote add origin git@github.com:rpflorence/example.com.git
 $ git push origin master
```

## Web Server: Clone the remote repository on your web server. You’ll need ssh access to it.

```git
 $ ssh rpflo@ryanflorence.com
 $ cd public_html
 $ git clone git@github.com:rpflorence/example.com.git
```

## Local Machine: Make changes to your local repository.

```git
 # make changes in your local repo
 $ git commit -a -m 'Added cool stuff'
 $ git push origin master
```

## Web Server: Pull the changes from the remote repository.

```git
 $ ssh rpflo@ryanflorence.com
 $ cd public_html/example.com
 $ git pull origin master
```

## Deployed!

I like this method because there is no real “setup,” it’s completely straightforward: clone the origin repository, 
pull updates from it, done. It’s not wearing fancy pants, it is simply git at its finest. 
For a method that is wearing fancy pants check out deploying websites with a tiny git hook

