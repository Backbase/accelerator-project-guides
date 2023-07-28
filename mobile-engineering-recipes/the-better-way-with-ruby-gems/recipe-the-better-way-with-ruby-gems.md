# Recipe - The Better Way with Ruby Gems
> This also solves a problem where Cocoapods fails to install dependencies in M1 machines.

# Background

Working with an iOS project involves the usage of tools such as Cocoapods, Slather, SwiftLint, Fastlane and more. Ideally, all engineers working on a particular codebase - as well as the CI machine it runs on - should maintain the same versions of these tools.

Some of these tools also use Ruby, and the version of Ruby installed in each one’s machine might lead to strange behaviours.

Consider, for a moment, you’ve aligned the versions of all the tools above, including Ruby itself, between everyone working on `CodebaseA`. What if you need to also work on `CodebaseB`, owned by another team possibly in another department? The chances of them using the exact same versions for all this tooling is very minimal.

# Solution

There is a way to maintain all these alignments, regardless of how many projects you’re working on, without changes to your global setup. A solution where, for every project, you can make use of different versions of Ruby, Cocoapods, Fastlane etc. Only by making use of:

*   Ruby Gems; and
    
*   [rbenv](https://github.com/rbenv/rbenv "https://github.com/rbenv/rbenv") - A Ruby version manager tool.
    

# Steps

## TL;DR:

Versions specified here are illustrative. Choose your versions carefully in accordance with your team.

### RBENV and Multiple Ruby Versions

1.  Install `rbenv`
    
    1.  `brew install rbenv`
        
2.  Initialise `rbenv` in your `~/.zshrc` or `~/.bashrc` file:
    
    1.  Add `eval "$(rbenv init - )"` to this file
        
    2.  Restart Terminal or run `source ~/.zshrc` (or `~/.bashrc`)
        
3.  Install a specific version of Ruby, with `rbenv`
    
    1.  `rbenv install 2.7.6`
        
4.  In a specific folder/repository, set the local version of Ruby
    
    1.  `rbenv local 2.7.6`
        
5.  Verify if the version of Ruby is now set correctly:
    
    1.  There should be a file named `.ruby-version`, whose content is the version you set
        
    2.  If you run `ruby --version`, it should point to the one you’ve just set
        
6.  Commit file `.ruby-version`
    

> If you use M1 and OS Ventura you could see errors like  
> **BUILD FAILED** (macOS 13.1 using ruby-build 20220726)  
> 
> Please try
> 
> 1.  `brew update && brew upgrade ruby-build`
>     
> 2.  `sudo rbenv install 2.7.6`
>     

### Ruby Gems

1.  Add your gems and their specific versions to file `Gemfile` (or create such file if it doesn’t exist):
    
    1.   ```shell
         source 'https://rubygems.org'
         
         gem 'cocoapods', '1.11.3'
         gem 'cocoapods-art'
         gem 'fastlane'
         gem 'slather'
        ```
        
2.  Install these gems with Bundler:
    
    1.   ```shell
         bundle install
        ```
        
    2.  You might have troubles with this installation. If that happens, you can specify a local path for bundler to install, with: `bundle config set --local path 'vendor/bundle'`
        
        Then proceed to run `bundle install` again.
        
3.  Commit the files `Gemfile` and `Gemfile.lock`
    
4.  No longer run the system installed versions of these tools. Use `bundle exec` instead.
    
    1.   ```shell
         bundle exec pod install
         bundle exec fastlane <YOUR-LANE>
        ```
        

> You can make this easier by creating an alias, in your `~/.zshrc` or `~/.bashrc` file, as seen below:
> 
> 1.   ```shell
>      alias bx='bundle exec'
>     ```
>     
> 2.  And run these with the shorter syntax:
>     
>      ```shell
>      bx pod install
>      bx fastlane <YOUR-LANE>
>     ```
>     

The steps below cover the same as the TL;DR above. It goes, however, deeper in explaining what you’re doing, how these things work and why we’re doing it this way.

**It’s recommended you read the sections below to have a better understanding of what you just did or are about to do.**

## 1\. Multiple Ruby versions

### 1.1 Install RBENV

RBENV is a Ruby version manager tool that allows us to install multiple versions of Ruby and set a specific version to a particular folder/repo/codebase. This way multiple versions can exist and multiple projects can use their own version if necessary.

If you don’t have RBENV installed, ensure the following:

> We recommend using `homebrew`, as it’s a package manager commonly used.

Simply run the following command in your Terminal:

 ```shell
 brew install rbenv
```

Once installed, we have to ensure it will be initiated properly. Add the following line to your `~/.zshrc` or `~/.bashrc` file:

 ```shell
 eval "$(rbenv init - )"
```

Save the file, restart your Terminal or run `source ~/.zshrc` or `source ~/.bashrc` (depending on which Shell you’re using).

Once you first install `rbenv`, the only version of Ruby available is the `system` version, which is whatever Ruby has been installed as system default. You can see the currently installed and available versions by running:

 ```shell
 rbenv versions
```

###   
1.2 Installing and Locking Ruby versions

A Ruby version is locked for a particular folder/repository with the presence of a file named `.ruby-version`, which contains solely the version of Ruby expected to be used, as in:

 ```shell
 2.7.6
```

> Simply stating a Ruby version in this file will require it to be installed, but won’t install it for you. Creating or updating this file doesn’t have to be done manually. The following steps will guide you on how to set it up.

As an example, we’ll lock the Ruby version for an iOS repository, called `PeachTreeBank-iOS`. Assume there is no current version lock for Ruby, and there is only the `system` version installed.

You can check the versions available for install with:

 ```shell
 rbenv install --list
```

For me, the output of this command is:

 ```shell
 2.6.10
 2.7.6
 3.0.4
 3.1.2
 jruby-9.3.6.0
 mruby-3.1.0
 picoruby-3.0.0
 rbx-5.0
 truffleruby-22.2.0
 truffleruby+graalvm-22.2.0
 
 Only latest stable releases for each Ruby implementation are shown.
 Use 'rbenv install --list-all / -L' to show all local versions.
```

> Notice the message at the bottom. These are stable releases and we recommend you choose one of them. For a bigger list, containing previews and unstable versions, use `--list-all`.

We can proceed to install version `2.7.6`:

 ```shell
 rbenv install 2.7.6
```

> Installing a Ruby version is only needed once. It is now available in your machine through `rbenv`, it will now appear when you run the command `rbenv versions`.

Now, with multiple versions available, you can specify which version this local folder/repository should use. Inside the folder, in Terminal, run:

 ```shell
 rbenv local <VERSION>
```

In this case:

 ```shell
 rbenv local 2.7.6
```

This will create the file `.ruby-version`. This file should be committed, so that it enforces this version for everyone else, including your CI machine.

> Later, if you want to change the version, make sure it’s installed with `rbenv` and run the `local` command again, i.e: `rbenv local 3.1.2`

## 2\. Using Ruby Gems

So far you’ve managed to have multiple versions of Ruby and set different versions for different repositories. Now, we’re set to use Ruby Gems.

Ruby Gems is a package manager, responsible for distributing Ruby programs and libraries. Programs such as Cocoapods and Fastlane.

It’ll facilitate our work by not relying on the versions of these tools we’ve installed in our machines directly, but have Ruby Gems handle it for us. This comes with the same benefit from the section above, the ability to maintain specific versions of it for different repositories.

### 2.1. Your repository already has a `Gemfile`

If your repository already has a file named `Gemfile`, it already uses Ruby Gems. You can simply specify the Gems you want to use in this file.

### 2.2. Your repository doesn’t have a `Gemfile`

Your repository doesn’t currently have a file named `Gemfile`. We’ll create one and proceed to install these gems.

It’s similar to a `Podfile`, where you’ll specify the dependencies and, optionally, their versions, with a certain constraint.

 ```ruby
 source 'https://rubygems.org'
 
 gem 'cocoapods', '1.11.3'
 gem 'cocoapods-art'
 gem 'fastlane'
```

The following will install `cocoapods` version `1.11.3` and the latest version of `cocoapods-art` that is compatible with `cocoapods`.

Save your file and run:

 ```shell
 bundle install
```

> You might have troubles with this installation. If that happens, you can specify a local path for bundler to install with: `bundle config set --local path 'vendor/bundle'`
> 
> Then proceed to run `bundle install` again.

> Once Ruby Gems are installed you’ll also have a `Gemfile.lock`, this file specifies the versions used during the latest installation/update and makes sure it locks to those versions for whoever is installing those dependencies, as long as they match the version constraints specified in the `Gemfile`.
> 
> `Gemfile.lock` should also be committed.

# 3\. Running your tools

By now your project has a locked version of Ruby, which everyone can install without conflicting with other setups/projects. And you have most of the tools you need, such as Cocoapods and Fastlane, installed in a similar manner, as Ruby Gems.

You can run these tools, within the folder/repository of your project, with `bundle exec`.

Instead of running:

 ```shell
 pod install
 fastlane <YOUR-LANE>
```

Run:

 ```shell
 bundle exec pod install
 bundle exec fastlane <YOUR-LANE>
```

You can make this easier by creating an alias in your `~/.zshrc` or `~/.bashrc` file:

 ```shell
 alias bx='bundle exec'
```

From now on, the commands above could be used as:

 ```shell
 bx pod install
 bx fastlane <YOUR-LANE>
```