---
layout: post
title: "How to build Ruydoop yourself"
date: 2013-07-22 20:49
comments: true
categories: hadoop jruby java rubydoop
---

Recently I come working with [Hadoop](http://hadoop.apache.org/) and the better Gem I've seen is [Rubydoop](https://github.com/iconara/rubydoop), but it has an issue with Hadoop 2.0.x, basically does not support it because has been changed its API.

<!-- more -->

I created an [issue](https://github.com/iconara/rubydoop/issues/9) and few days late received a [patch](https://github.com/iconara/rubydoop/issues/9#issuecomment-18968195) to fix it :D, but with a condition: I should build the .jar myself (and gem).


I’ll expect that you have set up hadoop 2.0.x.

### 1 - Clone repository

Very simple, you need clone the repository and switch to branch that has fix.
```
git clone git@github.com:iconara/rubydoop.git && \
cd rubydoop && \
git checkout hadoop_204
```

Branch [hadoop_204](https://github.com/iconara/rubydoop/commit/1db2054246fced0cba0f48ef330adf1b0a8c2f85) has commit.

### 2 - Export MY_RUBY_HOME
The [```Rakefile```](https://github.com/iconara/rubydoop/blob/hadoop_204/Rakefile#L15) expects that you have an environment variable called ```MY_RUBY_HOME```
```
export MY_RUBY_HOME=/path/home/jruby
```

The above path depends on your SO.

### 3 - Create .classpath

This file was created me more problems, because there is not nothing clean about what is the right content. In fact, I followed [suggestion made by @iconara](https://github.com/iconara/rubydoop/issues/9#issuecomment-21289355), but did not work for me.

This file must be created in root dir where you cloned repository.

As I mentioned earlier, this post expect that you have set up **hadoop 2.0.x**

What path I should put into ```.classpath``` file?

```
hadoop classpath
```

The previous command to show you the ```HADOOP_CLASSPATH```, basically where Hadoop has their jars files.

I needed to put two jars into ```.classpath``` to everything worked as expected.

Here my ```.classpath```

```
command
```

Once you set the right paths the rest it's very simple, you need run the following commands.

```
rake build
```

Whether everything is fine, the previous command should build ```lib/rubydoop.jar``` (reason of this post).

```
gem build rubydoop.gemspec
```

The above command will produce an output like:

```
// ouput
```

Last but not least, I suggest change the gem version, by this way you can add a specific version in your ```Gemfile``` and avoid conflict with current release version.

