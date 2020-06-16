# WannaGameCTF

## I) Cryptography
### 1.  Crypto warmup 1

Have you ever heard of something call rot13? synt{nuvuv_3n5l_e0g_Bx@ly4_6no7n9p7748opn9o13}
--------------------------------------------------------------------------------------------

Như đề bài đã gợi ý, chúng ta chỉ cần sử dụng [rot 13 deocder](https://rot13.com/) để lấy được flag

#### flag: flag{ahihi_3a5y_r0t_Ok@yl4_6ab7a9c7748bca9b13}

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

#### flag: flag{ahihi_an0th3r_r0ttEn_crYptO_192bc1a9d2aa2a3c}

### 3. Templed

When I visited Thailand, I discovered this weird ciphertext. Can you tell me what is was?
http://45.122.249.68:1338/ctf/crypto/templed/templed.png
--------------------------------------------------------------------------------------------


Mình không biết đây là kiểu mã hoá gì nên đã sử dụng https://images.google.com/ để tìm kiếm hình ảnh được cho.
và mình tìm thấy [The_Ciphers_of_the_Monks](https://en.wikipedia.org/wiki/The_Ciphers_of_the_Monks)

Vì format của đề là flag{....} nên mình chỉ cần decode từ ký tự thứ 6 đến ký tự gần cuối.
mình có được:
```python
c = [97, 104, 105, 104, 105, 95, 77, 48, 110, 49, 60, 95, 67, 105, 80, 104, 51, 114, 95, 49, 57, 57, 48, 50, 48, 50, 48]
```
Chuyển qua string ta có
```python
p = 'ahihi_M0n1<_CiPh3r_19902020'
```
#### flag: flag{ahihi_M0n1<_CiPh3r_19902020}

### 4. Substitute
I found an encrypted message written on the documents. Can you help me to decrypt it?
http://45.122.249.68:1338/ctf/crypto/subtitute.rar
-------------------------------------------------------------------------------------

Nhìn vào cipher text mình đoán nó được mã hoá theo kiểu Substitute, hoặc Vigenere. 
mình thử [Substitution Solver](https://www.guballa.de/substitution-solver)  và nhận được: 

flag{ahihi_s9MplE_subt9tUt2_cha9L2ngE_68d902e97921b98}

Nhưng đây không phải flag đúng, mình nhìn vào plain text của quá trình Solver vừa rồi có đoạn:
"Alan Mathison Turing OBE FRS (32 June 9193 – 5 June 9170)"
Điều này chứng tỏ các con số cũng đã bị thay đổi chứ không chỉ riêng các chữ cái. Mình tìm thêm thông tin về [Alan Turing](https://vi.wikipedia.org/wiki/Alan_Turing) rồi so sánh các thông tin trong plain text trong quá trình Solver vừa rồi và thông tin thực tế để có thể chuyển các con số về đúng như ban đầu.

#### flag: flag{ahihi_s1MplE_subt1tUt3_cha1L3ngE_86d143e15139b16}

### 5.  FoX OR Coyote?
Recently, I received challenge from the Wizard Fox. If you help me decrypt it, the Fox will give you a gift, wow!!!!!
http://45.122.249.68:1338/ctf/crypto/foxOrCoyote.rar
-----------------------------------------------------------------------------------------------------------------------

Xét hàm fox
```python
def fox(message, key):
    if len(message) != len(key):
        return False
    res = ""
    for i in range(0,len(message)):
        res += hex(ord(message[i]) ^ ord(key[i]))[2:]
    return res
 ```
 
ta thấy message và key đưa vào phải có cùng chiều dài. Hàm fox sẽ xor message và key lại với nhau và chuyển thành 1 hex string

```python
c = fox(msg, key)
if c == False:
    c = base64.b64encode(fox("flag{ahihi_sup3rrrrr_e@zYyyyyyy_FL4g_2033abd46dcd}",key))
else:
    c = base64.b64encode(c)
```
ta thấy chuỗi hex string trả về từ hàm fox sẽ được encode base64. Mình decode msg đề cho thì có được:
```python
c = '71a0be0a5d192d1d43555e44142d5e1e2b1e56530251243916234b31d3d2d5db41742135b5d5d114d578'
```
Mỗi byte khi chuyển về hex string thì nó có thể là 1 hoặc 2 ký tự, vậy nên mình viết 1 chương trình lần lượt thử xor 1 hoặc 2 ký tự trong chuỗi c với key

```python
msg = '71a0be0a5d192d1d43555e44142d5e1e2b1e56530251243916234b31d3d2d5db41742135b5d5d114d578'
key = 'avaluablepresentfromthewizardfoxneverdisappointyou'
p = ''

i = 0
j = 0
step = []

while 1:
    print('p: ', p)
    print('step: ', step)
    print('------------')
    print('1. +1')
    print('2. +2')
    print('3. tro lai')
    x = int(input('> '))
    if x == 1:      # xor 1 ký tự tiếp theo trong chuỗi hex msg và ký tự tiếp theo trong key
        p += chr(int(msg[j], 16)^ord(key[i]))
        i+=1
        j+=1
        step += [1]
    elif x == 2:       # xor 2 ký tự tiếp theo trong chuỗi hex msg và ký tự tiếp theo trong key
        p += chr(int(msg[j:j+2], 16)^ord(key[i]))
        i+=1
        j+=2
        step += [2]
    elif x == 3:        # xoá phần tử cuối cùng của p và step, phục hồi i và j
        p = p[:len(p)-1]
        j -= step[len(step)-1]
        i -=1
        step = step[:len(step)-1]
```
 
#### flag: flag{ahihi_x0000r_1s_v3rY_s1mpL3_huh_9b2f2c443e48}


### 6. Introduction to cRackStAtion
I found Steve's notes, maybe it revealed something. Help me crack this.
```python
p=13062677564290804543607941815269144346979431933838007043108570404376862452774318701990078529109955591222463710585668881344130971406862649685269784351617477
q=10380279115299028656077413808577423301042158756432887647724324479922120819706103537952532097362521128072591115998954321415941817172248970991411520402357357
e=65537
c=5756917440396671464628974240652786679202373069732281831941244227252323905441777334787995233336428621958051003871973316320207234224168095030205432224818931951058606345542382821128829521967522840438732908722111660663452320981333207849317189983658625363017228013707555953361811195850341177439941643597996821225
```
-----------------------------------------------------------
Đây là một bài [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)) đơn giản khi mà đã có tất cả những gì chúng ta cần

```python
import math
def getModInverse(a, m):
    if math.gcd(a, m) != 1:
        return None
    u1, u2, u3 = 1, 0, a
    v1, v2, v3 = 0, 1, m

    while v3 != 0:
        q = u3 // v3
        v1, v2, v3, u1, u2, u3 = (
            u1 - q * v1), (u2 - q * v2), (u3 - q * v3), v1, v2, v3
    return u1 % m
    
p=13062677564290804543607941815269144346979431933838007043108570404376862452774318701990078529109955591222463710585668881344130971406862649685269784351617477
q=10380279115299028656077413808577423301042158756432887647724324479922120819706103537952532097362521128072591115998954321415941817172248970991411520402357357
e=65537
c=5756917440396671464628974240652786679202373069732281831941244227252323905441777334787995233336428621958051003871973316320207234224168095030205432224818931951058606345542382821128829521967522840438732908722111660663452320981333207849317189983658625363017228013707555953361811195850341177439941643597996821225

# compute q
n = p*q

# Compute phi(n)
phi = (p-1)*(q-1)
 
  
# Compute modular inverse of e
d = getModInverse(e, phi)
print("d:  " + str(d))
# Decrypt ciphertext
pt = pow(c, d, n)

    
print('pt: ', pt)
print("text: " + bytearray.fromhex(str(hex(pt))[2:]).decode())
```

#### flag: flag{ahihi_b@by_RsA_r1ghT_2b0c2f8c99ab15}


### 7. Crackstation v2
Oh no! Steve has changed the numbers. However, the new mechanism is not secure at all.
http://45.122.249.68:1338/ctf/crypto/crackstation_v2.rar
-------------------------------------------------------------------------------------

Đề bài cho public key ở dạng pem, mình cần chuyển nó về text
```
openssl rsa -in key.pub -noout -text -pubin
```
kết quả:
```
RSA Public-Key: (576 bit)
Modulus:
    00:c2:cb:b2:4f:db:f9:23:b6:12:68:e3:f1:1a:38:
    96:de:45:74:b3:ba:58:73:0c:bd:65:29:38:86:4e:
    22:23:ee:eb:70:4a:17:cf:d0:8d:16:b4:68:91:a6:
    14:74:75:99:39:c6:e4:9a:af:e7:f2:59:55:48:c7:
    4c:1d:7f:b8:d2:4c:d1:5c:b2:3b:4c:d0:a3
Exponent: 65537 (0x10001)
```
ta có:
```python
    e = 65537
    n = 188198812920607963838697239461650439807163563379417382700763356422988859715234665485319060606504743045317388011303396716199692321205734031879550656996221305168759307650257059
```
vì p và q chỉ có 128 bit nên ta có thể dễ dàng [factor](http://factordb.com/) n thành p và q
```python
p=472772146107435302536223071973048224632914695302097116459852171130520711256363590397527

q=398075086424064937397125500550386491199064362342526708406385189575946388957261768583317
```
khi đã có p, q, e và cipher text rồi ta chỉ cần đơn giản là làm theo như bài "Introduction to cRackStAtion"

#### flag: flag{ahihi_cR@ck1ng_pUcL1c_k3y_iS_v3RY_eASY_0KL4_ab7c9a2d61e029b}

## II) Wep

### 1. Specical file
The File tells the search engine crawlers know the pages or files which the crawler can or can't request from your site.
http://10.104.0.100:1337, http://45.122.249.68:1337/
----------------------------------------------------------------------------














