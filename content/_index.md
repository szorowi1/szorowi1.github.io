---
# Leave the homepage title empty to use the site title
title: ''
date: 2022-10-24
type: landing

sections:
  - block: about.biography
    id: about
    content:
      title: About me
      # Choose a user profile to display (a folder name within `content/authors/`)
      username: admin
  - block: collection
    id: featured
    content:
      title: Featured Publications
      text: |- 
        {{% callout note %}}
        For a complete list of publications, please see my [Google Scholar](https://scholar.google.com/citations?user=3285IsEAAAAJ&hl=en) page.
        {{% /callout %}}
      filters:
        folders:
          - publication
        featured_only: true
    design:
      columns: '2'
      view: card
  - block: collection
    id: software
    content:
      title: Software
      filters:
        folders:
          - software
        featured_only: true
    design:
      columns: '2'
      view: card
---
