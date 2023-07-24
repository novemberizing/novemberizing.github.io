### 1.0.1
	* UPGRADE
		1. UPGRADE README
		2. Add image links to items listed on the index page.
			인덱스 페이지에 나열된 아이템들에 이미지 링크를 달자.

### 1.0.0
    * HOMEPAGE VERSION UP
        1. Upgrade about page [OK]
        2. Build a jekyll development environment using Docker [OK]
        3. Upgrade main page [OK]
        4. Jekyll exclude specified markdown file [OK]
        5. Let's make the social links fetched from _config.yml [OK]
        6. Remove old post. [OK]
        7. Let's automatically reload when the content changes [OK]
		8. README.md [OK]
		9. Create a blog page. [OK]
		10. Jekyll post not generated [OK]
		11. paginator.posts in blog.html doesn't work. [OK]
		12. First blog post
		13. Fixup blog link [OK]

### FUTURE
	* BUG FIX
		1. blog.html pagination not working in the github pages

> `jekyll-paginate-v2` is not supported by github pages.

### Solution

Rollback `jekyll-paginate`

_config.yml

```
...

paginate: 1
paginate_path: "/blog/page:num/"

...

plugins:
  - jekyll-paginate

...
```

### Link

[paginator.posts not working in Jekyll-paginate-v2 on Github Pages](https://stackoverflow.com/a/63363042)

    Just want to bring your attention that jekyll-paginate-v2 is not supported by GitHub pages.

		2. Warning:  github-pages can't satisfy your Gemfile's dependencies.
			Bundler can't satisfy your Gemfile's dependencies.
			Install missing gems with `bundle install`.
