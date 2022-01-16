# SCSS Notes

Base system colour preference site styling

```scss
/* UNIVERSAL STYLES */
@media (prefers-color-scheme: dark) {
  html {
    background-color: $black;
  }
  body {
    color: $white;
  }
  .title, .subtitle {
    color: $white;
  }
  a {
    color: #777676;
  }
}

@media (prefers-color-scheme: light) {
  html {
    background-color: $white;
  }
  body {
    color: $black;
  }
  .title, .subtitle {
    color: $black;
  }
  a {
    color: #454545;
  }
}
```
