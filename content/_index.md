---
# Leave the homepage title empty to use the site title
title: Site
date: 2022-10-24
type: landing

sections:

  - block: v1/about  #v1/about #about.avatar
    id: about
    content:
      # Choose a user profile to display (a folder name within `content/authors/`)
      username: admin
      # Override your bio text from `authors/admin/_index.md`?
    design:
      spacing:
        # Customize the section spacing. Order is top, right, bottom, left.
        padding: ["200px", "0", "200px", "0"]

#  - block: experience
#    content:
#      title: Education
#      # Date format for experience
#      #   Refer to https://wowchemy.com/docs/customization/#date-format
#      date_format: Jan 2006
#      # Experiences.
#      items:
#        - title: PhD in Life Sciences
#          company: University of Lincoln, United Kingdom
#          company_logo: uol
#          date_start: 2017-11-01
#          date_end: 2021-12-31
#        - title: MSc in Biological Sciences
#          company: Universidad de la República, Uruguay
#          company_logo: udelar
#          date_start: 2013-03-01
#          date_end: 2015-12-01
#        - title: BSc (Hons) in Biological Sciences
#          company: Universidad de la República, Uruguay
#          company_logo: udelar
#          date_start: 2006-03-01
#          date_end: 2012-02-01

      #   Add/remove as many experience `items` below as you like.
      #   Required fields are `title`, `company`, and `date_start`.
      #   Leave `date_end` empty if it's your current employer.
      #   Begin multi-line descriptions with YAML's `|2-` multi-line prefix.

    design:
      # Choose how many columns the section has. Valid values: '1' or '2'.
      columns: '2'
      spacing:
        # Customize the section spacing. Order is top, right, bottom, left.
        padding: ["40px", "0", "20px", "0"]

  - block: markdown
    id: parallax1
    design:
      background:
        image:
          # Name of image in `assets/media/`.
          filename: naturalistauy_01.jpeg
          # Apply image filters?
          filters:
            # Darken the image? Range 0-1 where 1 is transparent and 0 is opaque.
            brightness: 0.6
          #  Image fit. Options are `cover` (default), `contain`, or `actual` size.
          size: cover
          # Image focal point. Options include `left`, `center` (default), or `right`.
          position: center
          # Use a fun parallax-like fixed background effect on desktop? true/false
          parallax: true
          # Text color (true=light, false=dark, or remove for the dynamic theme color).
          text_color_light: true

  - block: collection
    id: publications
    content:
      title: Publications
      filters:
        folders:
          - publication
        exclude_featured: false
    design:
      columns: '2'
      view: citation

  - block: collection
    id: preprints
    content:
      title: Preprints
      filters:
        folders:
          - preprints
        exclude_featured: false
    design:
      columns: '2'
      view: citation

  - block: markdown
    id: parallax2
    design:
      background:
        image:
          # Name of image in `assets/media/`.
          filename: naturalistauy_01.jpeg
          # Apply image filters?
          filters:
            # Darken the image? Range 0-1 where 1 is transparent and 0 is opaque.
            brightness: 0.6
          #  Image fit. Options are `cover` (default), `contain`, or `actual` size.
          size: cover
          # Image focal point. Options include `left`, `center` (default), or `right`.
          position: center
          # Use a fun parallax-like fixed background effect on desktop? true/false
          parallax: true
          # Text color (true=light, false=dark, or remove for the dynamic theme color).
          text_color_light: true
      #spacing:
        # Customize the section spacing. Order is top, right, bottom, left.
        #padding: ["200px", "0", "100px", "0"]

  - block: portfolio
    id: projects
    content:
      title: Projects
      filters:
        folders:
          - project
      # Default filter index (e.g. 0 corresponds to the first `filter_button` instance below).
      default_button_index: 0
      # Filter toolbar (optional).
      # Add or remove as many filters (`filter_button` instances) as you like.
      # To show all items, set `tag` to "*".
      # To filter by a specific tag, set `tag` to an existing tag name.
      # To remove the toolbar, delete the entire `filter_button` block.
      buttons:
        - name: All
          tag: '*'
        - name: People and Nature
          tag: people-and-nature
        - name: Initiatives
          tag: initiatives
        - name: Thesis
          tag: thesis
        - name: Community Science
          tag: community-science
        - name: Molecular Ecology
          tag: molecular-ecology
        - name: Postdoc
          tag: postdoc
        - name: Computer Science
          tag: bioinformatics
        - name: Open Science
          tag: open-science
        - name: Other
          tag: Demo
    design:
      # Choose how many columns the section has. Valid values: '1' or '2'.
      columns: '1'
      view: masonry
      # For Showcase view, flip alternate rows?
      flip_alt_rows: true


  - block: markdown
    id: parallax3
    design:
      columns: '1'
      background:
        image:
          # Name of image in `assets/media/`.
          filename: naturalistauy_01.jpeg
          # Apply image filters?
          filters:
            # Darken the image? Range 0-1 where 1 is transparent and 0 is opaque.
            brightness: 0.6
          #  Image fit. Options are `cover` (default), `contain`, or `actual` size.
          size: cover
          # Image focal point. Options include `left`, `center` (default), or `right`.
          position: center
          # Use a fun parallax-like fixed background effect on desktop? true/false
          parallax: true
          # Text color (true=light, false=dark, or remove for the dynamic theme color).
          text_color_light: true

  - block: collection
    id: talks
    content:
      title: SciComm
      subtitle: Talks & other Activities
      count: 3
      filters:
        folders:
          - event
    design:
      columns: '2'
      view: compact


  - block: markdown
    id: parallax4
    design:
      background:
        image:
          # Name of image in `assets/media/`.
          filename: naturalistauy_01.jpeg
          # Apply image filters?
          filters:
            # Darken the image? Range 0-1 where 1 is transparent and 0 is opaque.
            brightness: 0.6
          #  Image fit. Options are `cover` (default), `contain`, or `actual` size.
          size: cover
          # Image focal point. Options include `left`, `center` (default), or `right`.
          position: center
          # Use a fun parallax-like fixed background effect on desktop? true/false
          parallax: true
          # Text color (true=light, false=dark, or remove for the dynamic theme color).
          text_color_light: true

  - block: collection
    id: blog
    content:
      title: Blog
      subtitle: A mix of coding, news and personal reflextions
      text: ''
      # Choose how many pages you would like to display (0 = all pages)
      count: 3
      # Filter on criteria
      filters:
        folders:
          - post
        author: ""
        category: ""
        tag: ""
        exclude_featured: false
        exclude_future: false
        exclude_past: false
        publication_type: ""
      # Choose how many pages you would like to offset by
      offset: 0
      # Page order: descending (desc) or ascending (asc) date.
      order: desc
    design:
      # Choose a layout view
      view: card
      columns: '2'

#  - block: tag_cloud
#    content:
#      title: Tags
#    design:
#      columns: '2'
#      spacing:
#        # Customize the section spacing. Order is top, right, bottom, left.
#        padding: ["20px", "0", "20px", "0"]


  - block: contact
    id: contact
    content:
      title: Contact
      subtitle:
      text: |-
      # Contact (add or remove contact options as necessary)
      email: grattarola[at]fzp.czu.cz
      address:
        street: Kamýcká 129
        city: Prague
        region: Suchdol
        postcode: 165 00
        country: Czech Republic
        country_code: CZ
      coordinates:
        latitude: 50.13
        longitude: 14.37
      # Automatically link email and phone or display as text?
      autolink: false
      # Email form provider
      <!-- form:
        provider: netlify
        formspree:
          id:
        netlify:
          # Enable CAPTCHA challenge to reduce spam?
          captcha: true -->
    design:
      columns: '2'

  - block: markdown
    id: parallax1
    design:
      background:
        image:
          # Name of image in `assets/media/`.
          filename: naturalistauy_01.jpeg
          # Apply image filters?
          filters:
            # Darken the image? Range 0-1 where 1 is transparent and 0 is opaque.
            brightness: 0.6
          #  Image fit. Options are `cover` (default), `contain`, or `actual` size.
          size: cover
          # Image focal point. Options include `left`, `center` (default), or `right`.
          position: center
          # Use a fun parallax-like fixed background effect on desktop? true/false
          parallax: true
          # Text color (true=light, false=dark, or remove for the dynamic theme color).
          text_color_light: true

---
