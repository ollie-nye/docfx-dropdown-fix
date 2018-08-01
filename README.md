# Dropdown Navbar Fix - Steps to reproduce the fix

1.  Download https://github.com/dotnet/docfx/releases/download/v2.37.2/docfx.zip
2.  Extract docfx.zip
3.  Run `docfx init -q` to get a new project
4.  `cd docfx_project`
5.  Add to `toc.yml`:

    ```YAML
    - name: More
      dropdown: true
      items:
      - name: Item 1
        topicHref: /abc
      - name: Item 2
        topicHref: /def
      - name: Item 3
        topicHref: /ghi
    ```
6.  Run `docfx template export default` to get the default template out of DocFX
7.  Replace the contents of `./custom_template/partials/li.html.tmpl` with:

    ```html
    <ul class="nav level{{level}}">
    {{#items}}
      <li>
        {{^dropdown}}
          {{^leaf}}
            <span class="expand-stub"></span>
          {{/leaf}}
          {{#topicHref}}
            <a href="{{topicHref}}" name="{{tocHref}}" title="{{name}}">{{name}}</a>
          {{/topicHref}}
          {{^topicHref}}
            <a>{{{name}}}</a>
          {{/topicHref}}
          {{^leaf}}
            {{>partials/li}}
          {{/leaf}}
        {{/dropdown}}
        {{#dropdown}}
          <li class="dropdown">
            <a class="dropdown-toggle" data-toggle="dropdown" href="#" role="button" aria-haspopup="true" aria-expanded="false">{{name}} <span class="caret"></span></a>
            <ul class="dropdown-menu level{{level}}">
              {{>partials/dd-li}}
            </ul>
          </li>
        {{/dropdown}}
      </li>
    {{/items}}
    </ul>
    ```
8.  Create `./custom_template/partials/dd-li.html.tmpl` and add:

    ```html
    {{#items}}
      <li><a href="{{topicHref}}">{{name}}</a></li>
    {{/items}}
    ```
9.  Stop active navbar items showing on the homepage with `./custom_template/styles/docfx.js`, replacing line 394:

    ```js
    $(e).addClass(active);
    ```

    with

    ```js
    if ((window.location.pathname != '/index.html') && (window.location.pathname != '/')) {
      $(e).addClass(active);
    }
    ```

10. Start site serve with `docfx docfx.json --serve`
