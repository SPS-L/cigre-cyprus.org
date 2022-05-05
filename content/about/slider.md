---
widget: slider
weight: 1
active: true
headless: true

design:
  # Slide height is automatic unless you force a specific height (e.g. '400px')
  slide_height: ''
  is_fullscreen: true
  # Automatically transition through slides?
  loop: false
  # Duration of transition between slides (in ms)
  interval: 2000

content:
  slides:
  - title: Founded
    content: CIGRE Cyprus established in 2008, is the National Committee and local representative organisation of CIGRE in Cyprus.
    align: center
    background:
      position: right
      color: '#666'
      brightness: 0.7
      media: solar-slider.jpg
  - title: Benefits
    content: 
    align: left
    background:
      position: center
      color: '#555'
      brightness: 0.7
      media: power-slider.jpg
    link:
      icon: fa-people-group
      icon_pack: fas
      text: Information
      url: './static/Cyprus_NC_Profile.pdf'
  - title: Members
    content: Currently, CIGRE Cyprus numbers 40 members, ranging from individuals to large scale companies (TSOC, DSOC, EAC, Vassiliko) and universities (UCY, CUT, Frederick University).
    align: right
    background:
      position: center
      color: '#333'
      brightness: 0.5
      media: slideshow-1.jpg
  - title: How to join!
    content: 
    align: right
    background:
      position: center
      color: '#333'
      brightness: 0.5
      media: slideshow-3.jpg
    link:
      icon: fa-people-group
      icon_pack: fas
      text: Join Us
      url: 'https://join.cigre.org'
---
