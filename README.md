##**Shopify** - Limit Product

This requires editing of some key Shopify templates, so please backup all the files you are working with to ensure you do not break anything. We will be editing the *product.liquid* and the *cart.liquid* files, as well as adding custom CSS and snippets.

---

####Step 1

Add the *snippet-limit-product.liquid* file to your **Snippets** folder.

Add the contents of *style.css* to your *custom-styles.css.liquid* file in the **Assets** folder.

####Step 2

Add snippet with warning about limited quantity to your *product.liquid* file via .liquid syntax. If using the provided snippet, you would add `{% include 'snippet-limit-product' %}` wherever you would like the warning to display.

####Step 3

Insert the following code into your **snippet-quantity.liquid** file as the max parameter in your **<input></input>** tag.

```html
{% for tag in product.tags %}{% if tag == "limited" %}max="5"{% endif %}{% endfor %}
```

For example:

```html
<input id="quantity" type="number" name="quantity" min="1" {% for tag in product.tags %}{% if tag == "limited" %}max="5"{% endif %}{% endfor %} value="1" />
```

This will prevent the user from adding more than **5** items with the tag *limited* to their cart

####Step 4

>This step requires editing your *cart.liquid* file. Please be careful as you might accidently break the checkout on your website. Backup the file before making any edits.

1.Find the `{% for %}` loop that generates the cart item table. It will usually look like this: `{% for item in cart.items %}`. Ignore the optional `{% if %}` statement you might have immediately following and look for the opening `<tr>` tag. If the tag is empty without any prperties or classes in it, simply replace with the following:

```html
<tr {% for tag in item.product.tags %}{% if tag == "limited" and item.quantity > 5 %}class="bs-callout bs-callout-warning"{% endif %}{% endfor %}>
```

This will wrap the entire table row in our custom styling to indicate an error.

2.Next, find the closing `</tr>` tag and add the following immediately after:

```html
{% for tag in item.product.tags %}{% if tag == "limited" and item.quantity > 5 %}
    <tr class="bs-callout bs-callout-warning">
        <td colspan="5">
            <b>Limit 5 per customer.</b>
        </td>
    </tr>
{% endif %}{% endfor %}
```

We are addint a tooltip to explain what threw the error, to the user, and how to correct it.

3.Let's add another tool tip directly above the checkout button with a `danger` style. Find the `<input type="submit" ...>` tag, and add the following immediately **above** it:

```html
{% for item in cart.items %}
    {% for tag in item.product.tags %}
        {% if tag == "limited" and item.quantity > 5 %}
            <div class="bs-callout bs-callout-danger">
                <b>Please fix the errors above before trying to continue.</b>
            </div>
        {% endif %}
    {% endfor %}
{% endfor %}
```

>You may have more than one `<input type="submit" ...>` tag right next to each other. Add the code above the first one, and check to see how it looks. The code in this step is simply visual and will not break anything.

4.Now we will disable the checkout button, unless all error are fixed. Find the `<input>` tag with `value="{{ 'cart.checkout' | t}}"` or something very similar. This is the button that takes you to the next checkout step so make sure you disable the right one. Add the following code inside the `<input>` tag:

```html
{% for item in cart.items %}{% for tag in item.product.tags %}{% if tag == "limited" and item.quantity > 5 %}disabled{% endif %}{% endfor %}{% endfor %}
```

5.Test, test, and test again.
