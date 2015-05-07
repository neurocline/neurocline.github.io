---
layout: post
title:  "Optimizing Follower Skills for Wow Missions"
date:   2015-05-06 17:54:00
categories: games
---
![My helpful screenshot](/assets/WowMission.jpg)

Wow 6.0 introduced Garrisons. One of the key features of Garrisons is sending your
followers on missions. And, while followers can have two skills, a single mission
could require 3 followers and 7 skills. The success of a mission depends on matching
followers skills' to the mission's required skills.

What's the best way to do this?

{% highlight python %}
# read the input

d = []
with open('missions.txt', 'rb') as source:
  next(source)
    for line in source:
      fields = line.rstrip('\n').split('\t')
      level = fields[1]
      if level == '100 (675)' or level == '100 (660)' or level == '100 (645)':
        d.append(sorted(filter(None, fields[6:13])))

allpairs = {}

# compute all pairs
for line in d:
  for i in range(0, len(line)):
    for j in range(i+1, len(line)):
      pairstr = line[i]+','+line[j]
      if pairstr not in allpairs:
        allpairs[pairstr] = 0
      allpairs[pairstr] += 1

# print by popularity
for k in sorted(allpairs, key=allpairs.get, reverse=True):
  print allpairs[k], k
{% endhighlight %}

This produces

* 11 Danger Zones,Group Damage
* 9 Massive Strike,Timed Battle
* 7 Group Damage,Minion Swarms
* 7 Danger Zones,Powerful Spell
* 7 Massive Strike,Powerful Spell
* 7 Group Damage,Massive Strike
* 7 Danger Zones,Minion Swarms
* 7 Minion Swarms,Wild Aggression
* 7 Group Damage,Wild Aggression
* ...
* 1 Magic Debuff,Wild Aggression
* 1 Magic Debuff,Magic Debuff
* 1 Massive Strike,Massive Strike
