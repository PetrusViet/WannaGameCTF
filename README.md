# WannaGameCTF

## I) Crypto
### 1.  Crypto warmup 1

Have you ever heard of something call rot13? synt{nuvuv_3n5l_e0g_Bx@ly4_6no7n9p7748opn9o13}
--------------------------------------------------------------------------------------------

Như đề bài đã gợi ý, chúng ta chỉ cần sử dụng [rot 13 deocder](https://rot13.com/) để lấy được flag

### flag: flag{ahihi_3a5y_r0t_Ok@yl4_6ab7a9c7748bca9b13}

### 2. Crypto warmup 2
Let's move to the next level. Can you decrypt it? bhwc{wdede_wj0pd3n_n0ppAj_ynUlpK_192xy1w9z2ww2w3y} 
--------------------------------------------------------------------------------------------

Nhìn có vẻ đây là kiểu caesar cipher. nó chỉ có 25 key nên mình có thể brute force 
```python
def encrypt(a, k):
    result = ''
    for i in a:
        if (not(ord(i) >= 64 and ord(i) <=  90) and not(ord(i) >= 97 and ord(i) <=  122)):
            result += i
        elif (i.isupper()):
             result += chr((ord(i) + k - 65) % 26 + 65)
        else:
             result += chr((ord(i) + k - 97) % 26 + 97)
    return result
    
ct = input('cipher-text: ')
for i in range(26):
    print('Key = ', i, ':', decrypt(ct, i))
    print('---------------------')
```

# flag: flag{ahihi_an0th3r_r0ttEn_crYptO_192bc1a9d2aa2a3c}

### 3. Templed

When I visited Thailand, I discovered this weird ciphertext. Can you tell me what is was?
http://45.122.249.68:1338/ctf/crypto/templed/templed.png
--------------------------------------------------------------------------------------------
