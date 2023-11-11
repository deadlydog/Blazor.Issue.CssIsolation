# Blazor Issue with CSS Isolation

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
If I uncomment it, it applies the rules the Radzen DataList as expected:

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
