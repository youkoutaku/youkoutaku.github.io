---
title: Writing a New Post
date: 2024-05-02 12:10 +0900
categories: [Journal]
tags: [Journal]
author: Youkoutaku
math: true
---

[Writing a New Post](https://chirpy.cotes.page/posts/write-a-new-post/)

## YAML
```yaml
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
## Math
```markdown
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

## Mermaid
```matlab
---
mermaid: true
---
```

## Images
### Caption
Images Caption
```markdown
![img-description](/path/to/image)
_Image Caption_
```

### Size
```markdown
![Desktop View](/assets/img/sample/mockup.png){: width="700" height="400" }
```

### Image Path
```yaml
---
img_path: /img/path/
---
```

### CDN URL
```_config.yml
img_cdn: https://cdn.com
```

## Preview image
```yaml
---
image:
  path: /path/to/image
  alt: image alternative text
---
```

## Pinned Posts
```yaml
---
pin: true
---
```

## Promptis
`prompt-{type}`

> Example line for prompt.
{: .prompt-tip }

```markdown
> Example line for prompt.
{: .prompt-tip }
```

> Example line for prompt.
{: .prompt-info }

```markdown
> Example line for prompt.
{: .prompt-info }
```

> Example line for prompt.
{: .prompt-warning }

```markdown
> Example line for prompt.
{: .prompt-warning }
```

> Example line for prompt.
{: .prompt-danger }

```markdown
> Example line for prompt.
{: .prompt-danger }
```

## Videos

## Jekyll
[Posts | Jekyll • Simple, blog-aware, static sites (jekyllrb.com)](https://jekyllrb.com/docs/posts/)

