# I can't open this file part 2

This write up assumes knowledge on solving part 1, as most of the code and algorithm is reused.

This challenge has two very similar approaches to solve, both involving a small brute force process and identifying the valid result.

First let's look at the encryption script.

```
if(cur < 10):
    encrypted = encrypted + '0' + str(cur)
```

This section make all numbers less than 10 to be in double-digits, e.g. 01, 02, 03 etc. This means that we need to split the encrypted data (numbers) into groups of 2. To do this, we can use the Python range() function.

`split_flag = [flag[i:i+2] for i in range(0, len(flag), 2)]`

Now let's go back to the previous line and check out the modulus operation:
`cur = (digits.find(c) + key) % 65`

Mod 65 means that digits.find(c) + key cannot be larger than 65, which also means the key cannot be larger than 65 (or rather, it CAN be more than 65, but it's cyclic, e.g. 66=1, 67=2 etc.), if we assume the index of c is 0.

Thus, we can come up with a brute force technique to find the correct key, by use a for loop to test out range(0, 66) as key. Note that because Python does not count in the upper-bound of the range() object, so we need to add 1 to 65.

Full script:

```
import base64
for key in range(0, 66):
    flag = open("flag.jpg.encrypted", "r").read()
    digits = "0123456789abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ+/="
    split_flag = [flag[i:i+2] for i in range(0, len(flag), 2)]
    ans = ''
    for i in split_flag:
        index = (int(i) + key) % 65
        flag += digits[index]
    flag = flag.encode('ascii')
    flag = base64.b64decode(flag)
    with open(f'flag{key}.jpg', 'wb') as f:
        f.write(flag)
```

After doing this, we can use the linux `file *.jpg` command to check the file type of the 65 generated jpg files. Only one will show as 'JPEG'.

Alternatively, you can add a check within the script so that it only outputs the image if the generated file header matches that of a JPEG file.