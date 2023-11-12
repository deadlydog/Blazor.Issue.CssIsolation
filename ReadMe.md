# Blazor Issue with CSS Isolation

> The solution to this issue is at the bottom of this page.

This is a sample project that reproduces an issue I am having where CSS Isolation is not working as expected in Blazor Server using some Radzen Blazor components.

You can see this in action by running the project and viewing the list on the main Index page.
The list is a Radzen DataList component, and CSS styles for it are defined in the [Index.razor.css](/src/Blazor.Issue.CssIsolation/Pages/Index.razor.css) file.

You can see in the [Index.razor.css](/src/Blazor.Issue.CssIsolation/Pages/Index.razor.css) file I have the following CSS, but the rules applied to the `.rz*` classes have no effect:

```css
/* This gets applied properly, so I know the Isolated CSS file itself is being applied. */
p {
  color: red;
}

/* For some reason these do not work from the Isolated CSS file, but do work from the wwwroot/css/site.css file */
/* Even using "!important" seems to have no effect when in the Isolated CSS file. */
.rz-datalist-data {
  background-color: blue;
}

.rz-g > div, .rz-datalist-data > li {
  padding: 0rem !important;
}
```

In the [wwwroot/css/site.css](/src/Blazor.Issue.CssIsolation/wwwroot/css/site.css) file I have the same CSS, but commented out.
If I uncomment it, it applies the rules to the Radzen DataList as expected:

```css
/* When these rules below are applied in this file, they work fine. They do not work when defined in an Isolated CSS file though. */
/* Uncommend the rules below to apply them. */
/*.rz-datalist-data {
  background-color: blue;
}

.rz-g > div, .rz-datalist-data > li {
  padding: 0rem;
}*/
```

## Problem screenshots

Here is what the page looks like when the CSS is applied from the Isolated CSS `Index.razor.css` file (not working properly):

![CSS Isolation Not Working](/docs/Images/using-isolated-css-the-rules-are-not-applied.png)

Here is what the page looks like when the CSS is applied from the `wwwroot/css/site.css`` file (works as expected):

![Global CSS file works fine](/docs/Images/using-global-css-the-rules-are-applied.png)

## Reaching out for help

I have reached out to the Radzen team for help [on their forums](https://forum.radzen.com/t/css-isolation-not-working-with-radzen-components/7147/1), as well as posted [a question on Stack Overflow](https://stackoverflow.com/questions/77466621/cannot-override-radzen-blazor-component-style-from-isolated-css-file).

## The solution

The Radzen team was able to provide me with [a solution to this issue](https://forum.radzen.com/t/css-isolation-not-working-with-radzen-components/7147/10?u=deadlydog).
The issue is not specific to Radzen controls, but rather a general issue with [Blazor CSS Isolation and child components](https://learn.microsoft.com/en-us/aspnet/core/blazor/components/css-isolation#child-component-support).

The solution is to add the `::deep` css pseudo-element in the Isolated CSS file to target the child components.
So the CSS in the [Index.razor.css](/src/Blazor.Issue.CssIsolation/Pages/Index.razor.css) file should be changed to:

```css
::deep .rz-datalist-data {
  background-color: blue;
}

::deep .rz-g > div, ::deep .rz-datalist-data > li {
  padding: 0rem;
}
```

That alone is not enough to fix the issue though.
We must also wrap the child control in an HTML element, such as a `<div>`, to get the generated CSS scope attribute.
So we must change the HTML in the [Index.razor](/src/Blazor.Issue.CssIsolation/Pages/Index.razor) file to:

```html
<div>
  <RadzenDataList ...>
    ...
  </RadzenDataList>
</div>
```

Once both changes have been applied the CSS Isolation works as expected 🙂
