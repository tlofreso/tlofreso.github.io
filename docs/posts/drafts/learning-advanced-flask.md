---
draft: true
date: 2024-04-04
authors:
  - me
---

# Learning Advanced Flask
I've been using Flask for years, but always at surface level. I'd go through the [Quickstart](https://flask.palletsprojects.com/en/3.0.x/quickstart/){target=_blank}, watch [Corey Schafer's](https://www.tlofreso.com/2024/01/06/people-i-follow/#corey-schafer){target=_blank} tremendous [Flask Series](https://youtube.com/playlist?list=PL-osiE80TeTs4UjLw5MM6OjgkjFeUxCYH&feature=shared){target=_blank}, and StackOverflow my way through projects. This has always been just fine! But Flask has stood the test of time... it's still very relevant, and deserves more of my attention. 

## Flask User's Guide
The [User's Guide](https://flask.palletsprojects.com/en/3.0.x/){target=_blank} is long, but very well done. I sailed through the tutorial in a few hours, and by the end had a functional and fully tested blog application. They didn't need to include testing, but chose to, and I really appreciate that.

## Keep Developing!

At the end of the tutorial, the documentation includes a page that I wish more tutorials would... A list of challenges titled "[Keep Developing!](https://flask.palletsprojects.com/en/3.0.x/tutorial/next/){target=_blank}" Encouraging users to enhance the application they just built with the following features:

 - A detail view to show a single post. Click a post’s title to go to its page.
 - Like / unlike a post.
 - Comments.
 - Tags. Clicking a tag shows all the posts with that tag.
 - A search box that filters the index page by name.
 - Paged display. Only show 5 posts per page.
 - Upload an image to go along with a post.
 - Format posts using Markdown.
 - An RSS feed of new posts.

This post documents my approach to developing each of these features. At least until I get bored and move onto the next thing. The code for this may be found [here](https://github.com/tlofreso/learn-advanced-flask){target=_blank}.

### Create a Detailed Post View
This first enhancement is fairly straight forward. We know we'll need a new view, and all the things that come with it: tests, routes, and templates. Here's my [commit](https://github.com/tlofreso/learn-advanced-flask/commit/a0bd1669bc06ed60b6736981b8994b4c5b973fd7){target=_blank} for this functionality. I added the following code to these three files:

=== "view_post.html"

    ``` html
    {% extends 'base.html' %}

    {% block header %}
      <h1>{% block title %}View "{{ post['title'] }}"{% endblock %}</h1>
    {% endblock %}

    {% block content %}
      <article class="post">
        <header>
          <div>
          <h1>{{ post['title'] }}</h1>
        <div class="about">by {{ post['username'] }} on {{ post['created'].strftime('%Y-%m-%d') }}</div>
      </div>
        {% if g.user['id'] == post['author_id'] %}
              <a class="action" href="{{ url_for('blog.update', id=post['id']) }}">Edit</a>
            {% endif %}
          </header>
        <p>{{ post['body'] }}</p>
      </article>
      <hr>
    {% endblock %}
    ```

=== "blog.py"

    ``` python
    @bp.route('/<int:id>/view', methods=('GET',))
    def view_post(id):
        post = get_post(id, check_author=False)
        return render_template('blog/view_post.html', post=post)
    ```

=== "test_blog.py"

    ``` python
    def test_view_post(client, auth):
        response = client.get('/1/view')
        assert b'test title' in response.data
        assert b'by test on 2018-01-01' in response.data
        assert b'test\nbody' in response.data
        assert b'href="/1/update"' not in response.data

        auth.login()
        response = client.get('/1/view')
        assert b'href="/1/update"' in response.data
    ```
Lastly, I made the `Title` a link to the homepage, and the `Post Title` a link to it's detailed page:

=== "base.html"

    ``` html
    <title>{% block title %}{% endblock %} - Flaskr</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
    <nav>
      <!-- replace this: <h1>Flaskr</h1>
      with this: -->
      <h1><a href="{{ url_for('index') }}">Flaskr</a></h1>
      <ul>
        {% if g.user %}
          <li><span>{{ g.user['username'] }}</span>
    ```

=== "index.html"

    ``` html
    <title>{% block title %}{% endblock %} - Flaskr</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
    <nav>
      <!-- replace this: <h1>{{ post['title'] }}</h1>
      with this: -->
      <h1><a href="{{ url_for('blog.view_post', id=post['id']) }}">{{ post['title'] }}</a></h1>
      <ul>
        {% if g.user %}
          <li><span>{{ g.user['username'] }}</span>
    ```

Onto the next feature.

### Like / Unlike a Post