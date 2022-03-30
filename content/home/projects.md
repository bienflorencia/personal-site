+++
# Projects widget.
widget = "projects"
active = true
date = 2016-04-20T00:00:00

title = "Projects"
subtitle = ""

# Order that this section will appear in.
weight = 30

# Content.
# Display content from the following folder.
# For example, `folder = "project"` displays content from `content/project/`.
folder = "project"

# View.
# Customize how projects are displayed.
# Legend: 0 = list, 1 = cards.
view = 1

# Widget layout
# Legend: 0 = two columns (default), 1 = single column
widget_layout = 0

# Filter toolbar.

# Default filter index (e.g. 0 corresponds to the first `[[filter]]` instance below).
filter_default = 0

# Add or remove as many filters (`[[filter]]` instances) as you like.
# Use "*" tag to show all projects or an existing tag prefixed with "." to filter by specific tag.
# To remove toolbar, delete/comment all instances of `[[filter]]` below.

[[filter]]
  name = "All"
  tag = "*"

[[filter]]
  name = "People and Nature"
  tag = "people-and-nature"

[[filter]]
    name = "Initiatives"
    tag = "initiatives"

[[filter]]
  name = "Thesis"
  tag = "thesis"

[[filter]]
    name = "Community Science"
    tag = "community-science"

[[filter]]
  name = "Molecular Ecology"
  tag = "molecular-ecology"

[[filter]]
    name = "Postdoc"
    tag = "postdoc"

[[filter]]
  name = "Computer Science"
  tag = "bioinformatics"

[[filter]]
  name = "Open Science"
  tag = "open-science"

+++
