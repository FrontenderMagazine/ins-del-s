# Comparing and contrasting ins, del, and s

Since the mists of HTML 2 we’ve been able to semantically indicate added or
removed information using the elements `<ins>` and `<del>` respectively. While
seemingly simple on the surface these elements have hidden depths. I’ll also
compare and contrast `<del>` with `<s>`, recently out of font style rehab and
back in HTML5. Let’s start with `<ins>` and `<del>`…

## Using `ins` and `del`

These two elements have very simple definitions:

> The `ins` and `del` elements represent edits to the document.

> The `ins` element represents an addition to the document.

> The `del` element represents a removal from the document.

> — [WHATWG HTML Living Specification][1]

So what kind of edits are we talking about? You could use these elements to:

* Record changes to an official document, such as meeting minutes or a law
* Note changes to an article — in addition to journalistic corrections, this 
helps to maintain editorial integrity and avoid charges of “rewriting history”
* Indicate the changes between two versions of a document (a diff), for example 
to indicate editorial changes
* Indicate a signficant addition or removal of content that people accessing the 
document would benefit from knowing about

While some uses require recording changes exactly, when you’re just highlighting
a change I think minor editing for clarity or grammar is fine — as long as the
*meaning* isn’t changed. If necessary consider adding a note describing the
change, perhaps in a footer or aside. Also, keep in mind that it rapidly becomes
impractical (or impossible) to mark up *all* edits — only use these elements
when there’s a benefit in explicitly indicating them.

### “Implied paragraph bondaries”

`<ins>` and `<del>` are unusual in that they can contain contain phrasing 
content or flow content (in HTML 4-speak, they can be inline or block-level). 
For example, here’s an inserted paragraph that contains a phrasing content 
deletion:

    <!-- block-level ins -->
    <ins>
      <!-- inline del -->
      <p>Today I saw <del>flying monkeys</del> the doctor. The prognosis was not good.</p>
    </ins>

Note that browsers will style these elements as inline by default, and you’ll
need to include `display: block;` if you want to apply block-level styling.
We’ll cover this [later in the article][2].

The spec warns that the elements “should not cross implied paragraph
boundaries”. These occur when there’s phrasing content (= text or inline
elements) without a block-level element wrapper as a sibling of a block-level
element. For example:

    <div>
      <p>This text is in a block-level element.</p>
      <!-- implied paragraph boundary here -->
      Oh snap! Someone forgot the paragraph tags!
    </div>

Of course, we *always* wrap our text, so this would never happen to us! But
still, now you know ^_^

We also need to make sure each `<ins>` or `<del>` element is either inline or
block-level, but **not** both. For example, don’t start in the middle of one
paragraph and end in the middle of the next.

    <!-- This is wrong -->
    <p>Flying monkeys are <del>a common hazard.</p>
    <p>Protect yourself by</del> screeching loudly.</p>

    <!-- del as inline -->
    <p>Flying monkeys are <del>a common hazard.</del></p>
    <p><del>Protect yourself by</del> screeching loudly.</p>

    <!-- del as block-level -->
    <del>
      <p>Flying monkeys are a common hazard.</p>
      <p>Protect yourself by screeching loudly.</p>
    </del>
    <p>Flying monkeys are screeching loudly.</p>

### `datetime` and `cite` attributes

The `<ins>` and `<del>` elements can also take two optional attributes,
`datetime` and `cite`. You’ll be familiar with `datetime` from [our article on
the `<time>` element][3], and it works just the same here. Use it to record the
date (and optionally the time) of the edit:

    <p><del datetime="2012-09-13">Todo: add a datetime example here.</del>  <ins datetime="2012-09-14T10:21:44+09:00">This inserted text has the date and time it was added indicated via the datetime attribute.</ins></p>

`cite` will also be familiar if you’ve read our article [Quoting and citing with 
`blockquote`, `q`, `cite`, and the `cite` attribute][4]. It contains a URL 
linking to more information about the change.

    <!-- Reference or in-page links are URLs too -->
    <ins cite="#ins-1">This inserted text has a cite attribute, with a link to more information.</ins>

As this is generally hidden data, there’s a strong chance of it becoming 
[metacrap][5]. You can help avoid this by exposing `cite` with CSS:

    <style>
    ins[cite]:after,
    del[cite]:after {
      content: " (" attr(cite) ")";
      font-size: 75%;
      line-height: 0;
      color: #888;
    }
    </style>
    …
    <ins cite="http://html5doctor.com/more-info/123">[Inserted text…] More information is available about this addition.</ins>

<ins cite="http://html5doctor.com/more-info/123">[Inserted text…] More information is available about this insertion.</ins>

*Example CSS to append the `cite` attribute’s value as generated content.*

Even better, consider adding this link as content, for example:

    <ins>This is an article update<a href="#ins-1">¹</a>.</ins>
    …
    <footer>
      <h2>Article changes</h2>
      <ol>
        <li id="ins-1">This addition was added on <time datetime="2012-09-14T10:21:44+09:00">14th September 2012</time> by Oli Studholme.</li>
      </ol>
    </footer>

Finally, make sure when using `<del>` that this is a removal from the document
you want/need to indicate. Other options include commenting the content out
using `<!-- -->`, or deleting it from the HTML source.

## The `s` element

The specification describes the `<s>` thus:

> The `s` element represents contents that are no longer accurate or no longer 
relevant.

> — [WHATWG HTML Living Specification][6]

Historically `<s>` (and the obsolete `<strike>`) were font style elements, and
they were depreciated in HTML 4, then dropped in HTML5. However, a couple of
semantic use cases have seen `<s>` return. These include marking up the original
price when something is on sale, and indicating a time limit that has passed,
such as early bird tickets for an event. This is content that provides useful
information so we don’t want to delete it, but it’s not accurate and we want to
indicate this. Other uses include any outdated but still relevant information
that isn’t the result of an edit. The `<s>` element could even arguably be used
for indicating sarcasm, or for marking up examples of [Sous rature][7] for all
you Jacques Derrida fans out there. Well… maybe ;-).

As an example, if Kickstarter used `<s>`, it might look like this:

![Example][Using the s element to semantically indicate when a limited pledge is full]

*Using the `<s>` element to semantically indicate when a limited pledge is full*

### `s` vs `del`: fight!

The `<s>` element seems dangerously close to stepping on the semantic toes of
`<del>`, as I’m sure someone will helpfully point out in the comments :)
However, there is a subtle but significant difference. `<ins>` and `<del>` are
for showing *editorial changes*, generally to an already published page. The
`<s>` element is for indicating information that is no longer relevant or
accurate, but still provides some value, and is generally used to create a
document with both old and new information *at the same time*.

Hixie describes the difference in this way:

> [T]o put it another way, with `<del>` there is usually a view of the document 
that doesn’t include the `<del>` text. But with `<s>` there is no version of the 
document without the `<s>` text, because the `<s>` text is part of the document 
still, it’s just that it’s marked as old.

> — Ian Hickson in the [WHATWG IRC channel][8]

## Styling `ins`, `del`, and `s`

The browser default styles are:

* `<ins>`: `text-decoration: underline` (default inserted content)
* `<del>`: `text-decoration: line-through` (default deleted content)
* `<s>`: `text-decoration: line-through` (default strikethrough content)

By using underline, the default `<ins>` styling is very similar to a visited
link, a classic usability blunder. Worse, the default `<del>` and `<s>` styles
are identical (although it’s rare that you’ll use them together). However, as
long as `<s>` is visually distinct from `<del>`, I’ve found the default 
`text-decoration: strikethrough;` style of `<s>` ends up being what you 
generally want.

### Phrasing `ins` and `del` styling

Here are some styles I’ve used for `<ins>` and `<del>` in the past:

#### `ins` styling

* Normal text with green underline (example inserted content)

    ins {
      border-bottom: 1px solid #47ad09;
      text-decoration: none;
    }

* Green text with faint underline (example inserted content)

    ins {
      border-bottom: 1px solid #ccc;
      text-decoration: none;
      color: #388807;
    }

* Normal text with a light green background color (example inserted content)

    ins {
      text-decoration: none;
      background-color: #d4fcbc;
    }

#### del styling

* Gray text (example deleted content)

    del {
      text-decoration: none;
      color: #777;
    }

* Red text (example deleted content)

    del {
      text-decoration: none;
      color: #91081f;
    }

* Normal or gray text with a light red background color (example deleted content)

    del {
      text-decoration: none;
      background-color: #fbb6c2;
      color: #555;
    }

Check your `<ins>` styling can’t be mistaken for a link. Make sure that if you
only use color to differentiate, that these are easily distinguished, even for
the colorblind. Avoid hiding `<del>` content with `display: none;` — if it’s no
longer content remove it or comment it out. Differentiate `<del>` from `<s>` if
you use both elements.

Finally, I prefer to avoid using `text-decoration: line-through;` for `<del>`,
as it makes the text hard to read. When it comes to `s` however, this default
style seems to be the best fit, which is just as well because nothing else seems
appropriate.

### Flow `ins` and `del` styling

By default, `<ins>` and `<del>` are treated as inline elements by browsers, and
styled as such. If you want to use block-level CSS, you’ll need to apply
`display: block;`. Also, even the nicer styles above can be a little strong for
whole paragraphs of text, not to mention useless for e.g. a block-level image. I
style `<ins>` and `<del>` for phrasing use, and use classes with overrides for
block-level use:

* Block-level `<ins>` with green left border

    .ins-block {
      display: block;
      margin-left: -1em;
      border-left: 0.5em solid #47ad09;
      /* styles to override inline ins style, e.g.
      text-decoration: none; */
      padding-left: 0.5em;
    }

Alice was beginning to get very tired of sitting by her sister on the bank and
of having nothing to do: once or twice she had peeped into the book her sister
was reading, but it had no pictures or conversations in it, “and what is the use
of a book,” thought Alice, “without pictures or conversations?”

* Block-level `<del>` with red left border and faded text

    .del-block {
      display: block;
      margin-left: -1em;
      border-left: 0.5em solid #91081f;
      /* styles to override inline del style, e.g.
      text-decoration: none; */
      padding-left: 0.5em;
      color: #777;
    }

So she was considering, in her own mind (as well as she could, for the hot day
made her feel very sleepy and stupid), whether the pleasure of making a daisy-
chain would be worth the trouble of getting up and picking the daisies, when
suddenly a White Rabbit with pink eyes ran close by her.

You could also append a label:

* Block-level `<ins>`: Green border with generated content label

    .labeled-ins-block {
      display: block;
      margin-left: -1.25em;
      border-left: 0.75em solid #47ad09;
      padding-left: 0.5em;
    }
    .labeled-ins-block:before {
      position: relative;
      display: block;
      content: "Addition";
      left: -1.55em; /* adjust to fit */
      top: 5em; /* adjust to fit */
      width: 0;
      height: 0;
      font: 0.6em/0 "Helvetica Neue", Arial, Helvetica, sans-serif;
      letter-spacing: 1px;
      color: #fff;
      -webkit-transform: rotate(270deg);
      -moz-transform: rotate(270deg);
      -ms-transform: rotate(270deg);
      -o-transform: rotate(270deg);
      transform: rotate(270deg);
    }

The rabbit-hole went straight on like a tunnel for some way, and then dipped
suddenly down, so suddenly that Alice had not a moment to think about stopping
herself before she found herself falling down what seemed to be a very deep
well.

Finally, WordPress users should note that my nemesis [`wpautop` will laugh at
your attempts][9] to use block-level `<ins>` and `<del>`. Make sure you use
“Format this post with: No Formatting”.

## Conclusion

So to sum up, we recommend you use `<ins>` and `<del>` for edits to an existing
document, but only when indicating these changes is beneficial for those using
the page. Use `<s>` to indicate information that is no longer relevant or
accurate, but removing that information would negatively affect the meaning.
Make sure to carefully style these elements to avoid confusion with links (for
`<ins>`), and each other (for `<del>` and `<s>`). Finally note that `<ins>` and
`<del>` can be ued for inline or block-level content (although not both at the
same time), but `<s>` can only be used for inline content.

I’ve noticed a trend towards not having dates as part of the article’s URL, with
authors then making a concious effort to keep articles up-to-date. If you do
this, `<del>` and `<ins>` change from being rarely used to quite useful for
indicating your edits. On the other hand, `<s>` seems destined to remain one of
those obscure but useful elements that you’ll hardly ever need, but will be glad
to have available when you do.

So what do you think? Do you use these elements? Will you use them now? Do you
have any other ways you like to style them? Let us know in the comments!

[1]: http://www.whatwg.org/specs/web-apps/current-work/multipage/edits.html
[2]: #flow-ins-del-css
[3]: http://html5doctor.com/the-time-element/
[4]: http://html5doctor.com/blockquote-q-cite/#cite
[5]: http://en.wikipedia.org/wiki/Metacrap
[6]: http://www.whatwg.org/specs/web-apps/current-work/multipage/text-level-semantics.html#the-s-element
[7]: http://en.wikipedia.org/wiki/Sous_rature
[8]: http://krijnhoetmer.nl/irc-logs/whatwg/20130409#l-123
[9]: http://core.trac.wordpress.org/ticket/8213

[Using the s element to semantically indicate when a limited pledge is full]: img/s-example.png