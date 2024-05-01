[Writing a New Post | Chirpy (cotes.page)](https://chirpy.cotes.page/posts/write-a-new-post/)

# YAML
```YAML
---
title: TITLE
date: YYYY-MM-DD HH:MM:SS +0900 #Tokyo
categories: [TOP_CATEGORIE, SUB_CATEGORIE]
tags: [TAG1, TAG2]  
author: <author_id>
math: true #Mathematical
img_path: /img/path/
---
```

---
# Math
```Markdown
<!-- Block math, keep all blank lines -->

$$
LaTeX_math_expression
$$

<!-- Equation numbering, keep all blank lines  -->

$$
\begin{equation}
  LaTeX_math_expression
  \label{eq:label_name}
\end{equation}
$$

Can be referenced as \eqref{eq:label_name}.

<!-- Inline math in lines, NO blank lines -->

"Lorem ipsum dolor sit amet, $$ LaTeX_math_expression $$ consectetur adipiscing elit."

<!-- Inline math in lists, escape the first `$` -->
1. \$$ LaTeX_math_expression $$
2. \$$ LaTeX_math_expression $$
3. \$$ LaTeX_math_expression $$
```

# Mermaid
```matlab
---
mermaid: true
---
```

# Images
## Caption

图像标题
```
![img-description](/path/to/image)
_Image Caption_
```

## Size
```
![Desktop View](/assets/img/sample/mockup.png){: width="700" height="400" }
```

## Image Path
```YAML
---
img_path: /img/path/
---
```

## CDN URL
```_config.yml
img_cdn: https://cdn.com
```

# Preview image
```yaml
---
image:
  path: /path/to/image
  alt: image alternative text
---
```

# Pinned Posts
```
---
pin: true
---
```

# Promptis
`prompt-{type}`
- `tip`
- `info`
- `warning`
- `danger`

```markdown
> Example line for prompt.
{: .prompt-info }
```

# Videos

# Jekyll
[Posts | Jekyll • Simple, blog-aware, static sites (jekyllrb.com)](https://jekyllrb.com/docs/posts/)

