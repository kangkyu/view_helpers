#"rails view helper output html"

http://guides.rubyonrails.org/form_helpers.html

[the Rails API documentation](http://api.rubyonrails.org/)

```
<%= form_tag do %>
  Form contents
<% end %>
```

assuming the current page is `/home/index`, the generated HTML will look like

```
<form accept-charset="UTF-8" action="/" method="post">
  <input name="utf8" type="hidden" value="&#x2713;" />
  <input name="authenticity_token" type="hidden" value="J7CBxfHalt49OSHp27hblqK20c9PgwJ108nDHX/8Cts=" />
  Form contents
</form>
```

The hidden input element has name attribute of utf8 enforces browsers to properly respect your form's character encoding and is generated for all forms whether their actions are "GET" or "POST".

 input element with name authenticity_token is a security feature of Rails called cross-site request forgery protection, and form helpers generate it for every non-GET form

[Security Guide](http://guides.rubyonrails.org/security.html#cross-site-request-forgery-csrf)


##a search form

```
<%= form_tag("/search", method: "get") do %>
  <%= label_tag(:q, "Search for:") %>
  <%= text_field_tag(:q) %>
  <%= submit_tag("Search") %>
<% end %>
```

+ `form_tag`
+ `label_tag`
+ `text_field_tag`
+ `submit_tag`

generates

```
<form accept-charset="UTF-8" action="/search" method="get">
  <input name="utf8" type="hidden" value="&#x2713;" />
  <label for="q">Search for:</label>
  <input id="q" name="q" type="text" />
  <input name="commit" type="submit" value="Search" />
</form>
```

+ form with "GET" method
+ label for input
+ text input
+ submit


##model object

```
@article = Article.new
```

```
<%= form_for @article, url: {action: "create"}, html: {class: "nifty_form"} do |f| %>
  <%= f.text_field :title %>
  <%= f.text_area :body, size: "60x12" %>
  <%= f.submit "Create" %>
<% end %>
```

```
<form accept-charset="UTF-8" action="/articles/create" method="post" class="nifty_form">
  <input id="article_title" name="article[title]" type="text" />
  <textarea id="article_body" name="article[body]" cols="60" rows="12"></textarea>
  <input name="commit" type="submit" value="Create" />
</form>
```
