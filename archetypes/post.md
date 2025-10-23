+++
# Default archetype for blog posts
# Fields: title, date, draft, tags, categories, summary

date = '{{ .Date }}'
draft = true
title = '{{ replace .File.ContentBaseName "-" " " | title }}'

# Optional default empty lists
# tags = []
# categories = []

# summary is optional
# summary = "A one-sentence summary of your post."
+++

Write your post here. Use markdown. You can include images under `static/` or `assets/`.
