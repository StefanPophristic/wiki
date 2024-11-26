---
layout: default
title: "SubFolder Land Page"
parent: "Guide to Updating the Wiki"
nav_order: 1
has_children: true
---

This is the land page for the subfolder. If you click on the "subFolder" button in the sidebar, it will take you here!


If you take a look at the code, the SubFolder.md file has the following header:
```
layout: default
title: "SubFolder Land Page"
parent: "Template"
nav_order: 2
has_children: true
```

- **Layout** specifies what kind of layout should be applied to this page. Currently we only have "default"
- **Title** is the title of this page, and if this page has children (subfolders), they will have to reference this title exactly
- **Parent** is the title of the folder directly above this one
- **nav-order**: in the navigation bar, in what order should the pages appear?
- **has_children**: will this page have subpages?
