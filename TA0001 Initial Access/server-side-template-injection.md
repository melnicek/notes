# [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Server%20Side%20Template%20Injection)

## bad characters

```
$
${
${{
${{<
${{<%
${{<%[
${{<%[%
${{<%[%'
${{<%[%'"
${{<%[%'"}
${{<%[%'"}}
${{<%[%'"}}%
```

## templating engine identification

![](https://gblobscdn.gitbook.com/assets%2F-L_2uGJGU7AVNRcqRvEi%2F-M7O4Hp6bOFFkge_yq4G%2F-M7OCvxwZCiaP8Whx2fi%2Fimage.png)

## have a look at documentation

Always look for the following, no matter the language or template engine:

1. How to start a print statement
2. How to end a print statement
3. How to start a block statement
4. How to end a block statement
