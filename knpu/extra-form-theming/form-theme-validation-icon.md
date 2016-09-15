# Form Theming: Add an Error Icon

Checkout Bootstrap's form documentation. Under validation, they have a cool little
feature: when your field has an error, you can add a cute little icon. I want a cute
icon! To get it, we just need to add a `has-feedback` class to the div around the
entire field *and* add the icon itself.

Right now, each field is surrounded by a div with a `form-group` class. How can we
*also* add a `has-feedback` class to this? Answer: override the block that's responsible
for rendering the *row* part of every field. In other words, the `form_row` block.

In `form_div_layout.html.twig`, search for the `form_row` block. There it is!
But, we might be overriding this in the bootstrap theme - so check there too. Yep,
we are: and this is where the `form-group` class comes from.

## Overriding a Block

Ok! So... how can we override this? Very simple. First, copy the block. Second, go
to your templates directory and create a new template called `_formTheme.html.twig`.
The name of this isn't file isn't important. And just to know when this is working,
add a class: `cool-class`.

Finally, we need to point Symfony to this new form theme template. And we already
know where to do this: right inside `config.yml`. After the bootstrap template, add
a new line with `_formTheme.html.twig`. Because this is *after* bootstrap, its blocks
will override the ones from bootstrap. Oh, and even though we don't have it explicitly
listed here, Symfony *always* uses `form_div_layout.html.twig` as the fallback file.

Ok, go back, and refresh! Inspect any form element. There it is! *Our* block is now
being used.

## Using Variables in Blocks

And here's where things get *really* interesting. We need to add a class to the div,
but *only* if this field has a validation error. Well check this out: this block
is *already* using a few variables, like `compound`, `force_error`  and `valid`.
But, where are those coming from? And what other stuff can we use?

It turns out that these are the *same* form variables that we could override from
the main, `_form.html.twig` template. Once you're inside of a form theme block, these
become *local* variables.

Let's see this in action: call `dump()` with no arguments, to dump *all* available
variables.

Refresh this page. Ah, now we have a big dump before *every* single field: revealing
all of the variables we have access to. And it doesn't matter *which* block you're
overriding: you always have access to this same, big group of variables. We can use
these to only add that `has-feedback` class *if* there is an error.

Remove the dump. Then, set a new variable called `showErrorIcon`. Copy *all* of the
logic from the if statement below that controls whether or not the `has-error` class
is added and paste it here. The most important variable is `valid`: if this is true,
the field failed validation. Don't worry about the `compound` variable - we'll talk
about it later.

Next, at the end of the `div`, use an inline if statement so that if `showErrorIcon`,
we add the `has-feedback` class. Then, to add the icon, add that same if statement
right after printing the widget. Add a span with the right classes to make this an
icon.

Ok, time to try it. Refresh! There's nothing yet, but there also aren't any validation
errors. Empty the name field and submit. Our Beautiful X!

But now, set the Subfamily field to "Select a Subfamily" and refresh. Ok, the drop-down
looks a little funny - the X is on top of the arrow. In fact, the Bootstrap docs
warn you about this: this icon should only be added to *text* fields. And other fields,
like checkboxes, will look even worse!

So, it's time to get a little smarter, and only add the cute icon for text fields.