### Drupal cacheability metadata

---

### Overview

Note:

- Assumed knowledge
- Background
- Max-age
- Context
- Tags

---

### Assumed knowledge

Note:

- Render arrays
- What caching is for

---

#### Render arrays

Stuff like this
<pre><code class="language-php hljs">
return [
  '#type' => 'item_list',
  '#items' => [
    $node->toUrl(),
  ],
];
</code></pre>

Note:

- Even this simple code has gotchas
- When should it vary
- How long can we cache it for
- When should we invalidate the cache

---

#### Caching

Note:

- Do something expensive, then save the state after the expensive work
- I had a performance problem, so I used caching, now I have 2 problems

---

### Max-age

Note:
- How long can something be cached

---

### Max-age

Back to this
<pre><code class="language-php hljs">
return [
  '#type' => 'item_list',
  '#items' => [
    $node->toUrl(),
  ],
];
</code></pre>

Note:

- This can be cached forever, there's no time-sensitive data

---

### Max-age continued

Consider something dynamic
<pre><code class="language-php hljs">
return [
  '#type' => 'item_list',
  '#items' => [
    $this->formatTimeDifference($timestamp),
  ],
];
</code></pre>

Note:

- This will expire fairly fast

---

### Max-age continued

Like so.
<pre><code class="language-php hljs">
$metadata = new CacheabilityMetadata();
$metadata->setMaxAge(60);
$build = [
  '#type' => 'item_list',
  '#items' => [
    $this->formatTimeDifference($timestamp),
  ],
];
$metadata->applyTo($build);
return $build;
</code></pre>

Note:

- This will expire fairly fast

---

### Contexts

When does the render array need to be varied

---

### Contexts continued

Back to this
<pre><code class="language-php hljs">
return [
  '#type' => 'item_list',
  '#items' => [
    $node->toUrl(),
  ],
];
</code></pre>

Note:

- This might vary per user
- What if the node isn't published?
- What if there are access controls?

---

### Contexts continued

You may think to do this
<pre><code class="language-php hljs">
$metadata = new CacheabilityMetadata();
$metadata->setCacheContexts(['user.permissions']);
$build = [
  '#type' => 'item_list',
  '#items' => [
    $node->toUrl(),
  ],
];
$metadata->applyTo($build);
return $build;
</code></pre>

Note:

- You can manually add context
- But there's a smarter way

---

### Contexts continued

But instead entities and many other items provide this for you
<pre><code class="language-php hljs">
$metadata = new CacheabilityMetadata();
$metadata->addCacheableDependency($node);
$build = [
  '#type' => 'item_list',
  '#items' => [
    $this->formatTimeDifference($timestamp),
  ],
];
$metadata->applyTo($build);
return $build;
</code></pre>

Note:

- This doesn't just do contexts, but max age and tags too

---

### Contexts continued

What can you use for <code>->addCacheContexts</code>

Anything that implements <code>\Drupal\Core\Cache\Context\CacheContextInterface</code>

Examples:

- user.permissions 
- url.query_args
- url.path

---

### Contexts continued

Specificity

- user.permissions is less specific than user
- url.path is less specific than url

Be careful

Note:

- Too specific = too many records and low hit rate

--- 

### Tags

When does the entry need to be invalidated

---

### Tags continued

Back to this
<pre><code class="language-php hljs">
return [
  '#type' => 'item_list',
  '#items' => [
    $node->toUrl(),
  ],
];
</code></pre>

Note:

- When does this need to be invalidated?

---

### Tags continued

You can use ->addCacheTags

e.g. node:3

Note:

- But you probably won't

---

### Tags continued

Because entities and many other items provide this for you
<pre><code class="language-php hljs">
$metadata = new CacheabilityMetadata();
$metadata->addCacheableDependency($node);
$build = [
  '#type' => 'item_list',
  '#items' => [
    $this->formatTimeDifference($timestamp),
  ],
];
$metadata->applyTo($build);
return $build;
</code></pre>

---

### Tags continued

Tags can be specific

node:3

---

### Tags continued

Tags can be broad

node_list

---

### Summing up

- Caching speeds things up
- Knowing when an entry expires = Max-Age
- Knowing when an entry varies = Contexts
- Knowing when an entry is invalid = Tags


