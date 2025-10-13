---
layout: default
title: "2. Filtering"
parent: "Preprocessing"
nav_order: 2
has_children: true
section: "MEG"
---

# Filtering
Filtering is one of the first steps we do when preprocessing data. The idea behind filtering is simple: a filter assigns output as a weighted transformation of the input. More often than not, we use filters to select data of interest and discard everything else as noise.

There are two types of filtering that can be run on MEEG data: **spatial filters** and **temporal filters**. While spatial filters transform spatial information (think channels or electrodes), temporal filters transform temporal information.

This section will largely cover different filter types and considerations in temporal filtering and cover PCA and interpolation in spatial filtering.
