Sometimes you've run your `knitr` document, and you'd just like to examine some of the cached data.  It's not obvious how to do that, and this probably isn't really what `knitr` was intended for but it can be done by using the `lazyLoad()` function.  If you look in the `knitr` 'cache' directory there should be a load of `.rdb`, `.rdx` and `.RData` files that store all of the cached computation. A call along the lines of

```
lazyLoad("name-of-my-cached-rdx-file")
```

does the trick.  Note the lack of a file extension.

