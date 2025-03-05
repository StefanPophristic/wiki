---
layout: default
title: "Stefan_1"
parent: "Guide to Updating the Wiki"
nav_order: 4
has_children: false
---

# Example

This is my page!

It will reference a global variable {{site.title}} and {{site.maintainer}}

It will contain an image!

![And this is a description!](../../../images/example.jpg)
![And this is a description!](../images/example.jpg)


And I want to link to [this site right here](https://jekyllrb.com/docs/variables/).

# Lab Members
And here I want to list the lab members! I will add liquid syntax and a yaml file to do so.

{% assign allPeople = site.data.lab_members %}

{% for person in allPeople %}

[{{person.name}}]({{person.website}}) is a {{person.year}} student focusing on {{person.focus}}.

{% endfor %}
