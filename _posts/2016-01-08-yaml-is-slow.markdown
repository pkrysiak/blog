---
title:  "Yaml is SLOW !"
date:   2016-02-23 14:00
categories: [yaml, serialization]
tags: [yaml, serialization, yajl, oj, marshal, json]
---

It is very common tendency in ruby world to use Yaml. Probably every ruby/rails programmer already used it in one of the projects. Either to modify `config.yml`, or maybe to make some translations for various languages using `I18n` gem.
Nothing weird... Rails speaks Yaml natively, you don't have to include any fancy gems or follow any other convention, the format is very readable. Everything works more or less beautiful till the time when:

### You decide to use it as [serialization](https://en.wikipedia.org/wiki/Serialization) mechanism. ###
{: style="color:gray; text-align: center; padding-bottom: 20px"}

We all know that ruby performance sucks a little bit. But by choosing mechanisms that put so much overhead on the performance we will fail even more one day.

Currently I'm working in one project that had quite interesting problem with [Delayed Job](https://github.com/collectiveidea/delayed_job) that uses Yaml as serializer.
Searching for the problem and digging around i found out that serialization process takes a lot of time.
But... that is the story related to this post that i will tell you another time.

For the purpose of choosing the most efficient [serialization](https://en.wikipedia.org/wiki/Serialization) mechanisms and inspired by the solutions that i found already in the network, i wrote simple comparator
that will give you idea how much processing power it consumes when doing very simple task.

You can see it here: [ruby serialization comparator](https://github.com/pawel-krysiak/ruby-serialization-comparator)

The results of the test looks like this:

``` ruby
Serialization speed test:
YAML:    43.5923869609833 sec
JSON:    6.46457982063293 sec
OJ Json: 2.97412514686584 sec
Yail:    8.06042385101318 sec
Marshal: 3.18873286247253 sec
```

The conclusions that i got from this experiment are: `Yaml` can be good as notation for the various config files. It follows similar indentation ideology as `ruby/python`, and that makes it very readable, but there is a lot to be improved in matters of it's performance,
therefore please make sure that you think twice before you will use it for the similar job.