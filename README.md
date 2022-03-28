## Welcome to Elasticsearch Training Snippets

### Work With Index

Create an index

```markdown
PUT /containers
```

Get an index

```markdown
GET /containers
```

Find an index

```markdown
HEAD /containers
```

Close an index

```markdown
POST /containers/_close
```

Update settings of an index

```markdown
PUT /containers/_settings
{
      "index" : {
         "number_of_shards" : 3,
         "number_of_replicas" : 2
      }
}
```
Open an index
```markdown
POST /containers/_open
```
Delete an index
```markdown
DELETE /containers
```

For more details see [Basic writing and formatting syntax](https://docs.github.com/en/github/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/victorzhang428/Elastic/settings/pages). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://docs.github.com/categories/github-pages-basics/) or [contact support](https://support.github.com/contact) and we’ll help you sort it out.
